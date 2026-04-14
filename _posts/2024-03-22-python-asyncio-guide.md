---
title: "Python 异步编程：从 asyncio 到实际生产应用"
date: 2024-03-22
categories: [后端开发]
tags: [Python, asyncio, 性能优化, 并发]
excerpt: "asyncio 已经成为 Python 高性能 I/O 场景的标配。本文从核心概念出发，结合实际案例讲解如何正确使用异步编程。"
---

## 为什么要用异步编程？

对于 I/O 密集型任务（HTTP 请求、数据库查询、文件读写），传统的同步代码会让线程阻塞等待，而异步编程允许在等待期间处理其他任务，显著提升吞吐量。

```
同步（Synchronous）：
  任务A [====等待IO====] [处理] 
  任务B               [====等待IO====] [处理]
  
异步（Asynchronous）：
  任务A [IO发出] -----------> [处理]
  任务B   [IO发出] -------> [处理]
  任务C     [IO发出] -----------> [处理]
```

## 核心概念速览

### 协程（Coroutine）

```python
import asyncio

async def fetch_data(url: str) -> dict:
    # 这是一个协程函数
    await asyncio.sleep(1)  # 模拟 I/O 等待
    return {"url": url, "status": 200}

# 运行协程
result = asyncio.run(fetch_data("https://example.com"))
```

### 并发执行多个协程

```python
import asyncio
import httpx

async def fetch_url(client: httpx.AsyncClient, url: str) -> dict:
    resp = await client.get(url)
    return {"url": url, "status": resp.status_code, "size": len(resp.content)}

async def main():
    urls = [
        "https://httpbin.org/get",
        "https://httpbin.org/ip",
        "https://httpbin.org/headers",
    ]
    
    async with httpx.AsyncClient(timeout=10) as client:
        # gather 并发执行，等待所有完成
        results = await asyncio.gather(
            *[fetch_url(client, url) for url in urls]
        )
    
    for r in results:
        print(f"{r['url']} → {r['status']} ({r['size']} bytes)")

asyncio.run(main())
```

### 任务（Task）与取消

```python
async def long_running_task(name: str):
    try:
        print(f"{name} 开始")
        await asyncio.sleep(10)
        print(f"{name} 完成")
    except asyncio.CancelledError:
        print(f"{name} 被取消，执行清理...")
        raise  # 必须重新抛出 CancelledError

async def main():
    task = asyncio.create_task(long_running_task("任务A"))
    
    await asyncio.sleep(2)
    task.cancel()  # 2秒后取消
    
    try:
        await task
    except asyncio.CancelledError:
        print("任务已取消")
```

## 常见陷阱与解决方案

### 1. 阻塞调用拖慢事件循环

```python
# ❌ 错误：同步阻塞调用
async def bad_example():
    import time
    time.sleep(5)  # 阻塞整个事件循环！

# ✅ 正确：将阻塞调用放入线程池
async def good_example():
    loop = asyncio.get_event_loop()
    await loop.run_in_executor(None, time.sleep, 5)
    
    # 或使用 asyncio.to_thread（Python 3.9+）
    await asyncio.to_thread(time.sleep, 5)
```

### 2. 不当的异常处理

```python
# ❌ gather 默认行为：一个失败不影响其他
results = await asyncio.gather(task1(), task2(), task3())
# 若 task2 抛出异常，task1/task3 的结果仍返回

# ✅ 需要收集所有结果（包括异常）
results = await asyncio.gather(
    task1(), task2(), task3(),
    return_exceptions=True  # 异常作为返回值而非抛出
)
for r in results:
    if isinstance(r, Exception):
        print(f"任务失败: {r}")
```

### 3. 共享状态的并发安全

```python
import asyncio

class AsyncCounter:
    def __init__(self):
        self._value = 0
        self._lock = asyncio.Lock()
    
    async def increment(self):
        async with self._lock:
            # 临界区：保证原子性
            old = self._value
            await asyncio.sleep(0)  # 模拟切换点
            self._value = old + 1
    
    @property
    def value(self):
        return self._value
```

## 生产实践：FastAPI + SQLAlchemy 异步

```python
from fastapi import FastAPI, Depends
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "postgresql+asyncpg://user:pass@localhost/db"
engine = create_async_engine(DATABASE_URL, pool_size=20, max_overflow=0)
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)

app = FastAPI()

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/{user_id}")
async def get_user(user_id: int, db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User).where(User.id == user_id))
    user = result.scalar_one_or_none()
    if not user:
        raise HTTPException(404, "用户不存在")
    return user
```

## 性能基准参考

| 场景 | 同步（线程池） | 异步（asyncio） |
|------|---------------|----------------|
| 100 并发 HTTP 请求 | ~12s | ~1.2s |
| 1000 并发 DB 查询 | OOM / 超时 | ~3s |
| CPU 密集型计算 | 更快（GIL 释放） | 无优势 |

> **原则**：异步适合 I/O 密集，CPU 密集型任务仍然使用多进程（`multiprocessing` 或 `ProcessPoolExecutor`）。

## 总结

- 使用 `asyncio.gather` 并发执行独立 I/O 任务
- 阻塞调用必须用 `run_in_executor` 或 `asyncio.to_thread` 包装
- 使用 `asyncio.Lock` 保护共享状态
- 生产环境优先选用 `httpx`、`asyncpg`、`aioredis` 等成熟异步库
