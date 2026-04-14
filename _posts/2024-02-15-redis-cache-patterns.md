---
title: "Redis 缓存设计：穿透、击穿、雪崩的预防与解决"
date: 2024-02-15
categories: [系统设计]
tags: [Redis, 缓存, 高可用, 分布式]
excerpt: "缓存三大经典问题：穿透、击穿、雪崩。深入分析每种场景的成因，并给出生产可用的解决方案。"
---

## 背景

引入缓存层后，系统的读性能得到大幅提升。然而在高并发场景下，三类典型问题可能让缓存形同虚设，甚至拖垮数据库：

- **缓存穿透（Cache Penetration）** — 查询根本不存在的数据
- **缓存击穿（Cache Breakdown）** — 热点 Key 在高并发时过期
- **缓存雪崩（Cache Avalanche）** — 大量 Key 同时失效

## 一、缓存穿透

### 问题描述

恶意请求（或代码 Bug）频繁查询数据库中不存在的 Key，每次都绕过缓存直打数据库。

```
请求: GET /users/99999999 (不存在)
→ 查 Redis: MISS
→ 查 DB: NULL
→ 不写缓存
→ 下次请求仍然穿透...
```

### 解决方案

**方案 1：缓存空值**

```python
async def get_user(user_id: int) -> Optional[dict]:
    cache_key = f"user:{user_id}"
    cached = await redis.get(cache_key)
    
    if cached is not None:
        if cached == "NULL":  # 空值标记
            return None
        return json.loads(cached)
    
    user = await db.fetch_user(user_id)
    
    if user is None:
        # 缓存空值，TTL 设置较短（如 60s）
        await redis.setex(cache_key, 60, "NULL")
        return None
    
    await redis.setex(cache_key, 3600, json.dumps(user))
    return user
```

**方案 2：布隆过滤器（推荐大规模场景）**

```python
from bloom_filter2 import BloomFilter

# 系统启动时加载所有合法 ID 到布隆过滤器
bloom = BloomFilter(max_elements=10_000_000, error_rate=0.001)

async def init_bloom_filter():
    async for user_id in db.stream_all_user_ids():
        bloom.add(str(user_id))

async def get_user_v2(user_id: int) -> Optional[dict]:
    # 布隆过滤器快速判断
    if str(user_id) not in bloom:
        return None  # 一定不存在，直接返回
    
    # 正常缓存流程...
    return await get_user(user_id)
```

> **布隆过滤器特性**：可能误判"存在"（假阳性），但绝不会误判"不存在"，适合用于过滤不存在的数据。

## 二、缓存击穿

### 问题描述

某个热点 Key 过期的瞬间，大量并发请求同时发现缓存 MISS，全部打到数据库。

```
t=0: 热点Key "hot:article:1" 过期
t=1ms: 1000 个请求同时 MISS → 1000 个数据库查询
```

### 解决方案

**方案：互斥锁（Mutex）**

```python
import asyncio

_locks: dict[str, asyncio.Lock] = {}

async def get_article(article_id: int) -> dict:
    cache_key = f"article:{article_id}"
    
    # 1. 先尝试从缓存获取
    cached = await redis.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # 2. 缓存 MISS，获取本地锁（防止同进程内重复请求）
    if cache_key not in _locks:
        _locks[cache_key] = asyncio.Lock()
    
    async with _locks[cache_key]:
        # 3. Double-check：可能其他协程已经回填
        cached = await redis.get(cache_key)
        if cached:
            return json.loads(cached)
        
        # 4. 真正查数据库
        article = await db.fetch_article(article_id)
        await redis.setex(cache_key, 3600, json.dumps(article))
        return article
```

**方案 2：逻辑过期（不设置 TTL）**

```python
import time

async def get_article_logical_expire(article_id: int) -> dict:
    cache_key = f"article:{article_id}"
    cached = await redis.get(cache_key)
    
    if cached:
        data = json.loads(cached)
        # 检查逻辑过期时间
        if data["__expire_at"] > time.time():
            return data["value"]
        
        # 已逻辑过期：异步刷新，返回旧数据（牺牲一致性，换取高可用）
        asyncio.create_task(_refresh_article(article_id, cache_key))
        return data["value"]  # 返回旧数据，不阻塞
    
    # 缓存不存在：同步加载
    article = await db.fetch_article(article_id)
    payload = {"value": article, "__expire_at": time.time() + 3600}
    await redis.set(cache_key, json.dumps(payload))
    return article
```

## 三、缓存雪崩

### 问题描述

大量 Key 在同一时间段内集中失效（例如系统启动后同时写入，TTL 相同），或 Redis 节点宕机，导致数据库瞬间承受巨大压力。

### 解决方案

**1. TTL 随机化**

```python
import random

BASE_TTL = 3600  # 1 小时

async def set_cache(key: str, value: dict):
    # 在基础 TTL 上加随机抖动，避免集中失效
    jitter = random.randint(-300, 300)  # ±5 分钟
    ttl = BASE_TTL + jitter
    await redis.setex(key, ttl, json.dumps(value))
```

**2. 多级缓存**

```python
from functools import lru_cache

# L1: 进程内缓存（最快，容量小）
# L2: Redis 分布式缓存
# L3: 数据库

async def get_config(key: str) -> str:
    # L1
    local = local_cache.get(key)
    if local:
        return local
    
    # L2
    remote = await redis.get(f"config:{key}")
    if remote:
        local_cache.set(key, remote, ttl=30)
        return remote
    
    # L3
    value = await db.fetch_config(key)
    await redis.setex(f"config:{key}", 3600, value)
    local_cache.set(key, value, ttl=30)
    return value
```

**3. Redis 高可用部署**

- **Sentinel 模式**：主从 + 自动故障转移，适合中小规模
- **Cluster 模式**：数据分片，水平扩展，适合大规模
- **只读副本**：读写分离，降低主节点压力

## 方案对比总结

| 问题 | 推荐方案 | 适用场景 |
|------|---------|---------|
| 缓存穿透 | 布隆过滤器 | 数据量大、攻击风险高 |
| 缓存穿透 | 缓存空值 | 数据量小、实现简单 |
| 缓存击穿 | 互斥锁 | 强一致性要求 |
| 缓存击穿 | 逻辑过期 | 高可用优先、可接受短暂旧数据 |
| 缓存雪崩 | TTL 随机化 | 通用，必选 |
| 缓存雪崩 | 多级缓存 | 读多写少的配置类数据 |
| 缓存雪崩 | Redis 高可用 | 生产环境标配 |

> 缓存设计没有银弹，需要根据业务的**一致性要求**、**可用性要求**和**数据规模**综合选择。
