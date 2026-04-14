---
title: "用 Docker Compose 搭建本地开发环境的最佳实践"
date: 2024-04-10
categories: [运维与部署]
tags: [Docker, DevOps, 工程效率]
excerpt: "一套规范的 Docker Compose 配置可以让团队成员在分钟级完成本地环境初始化，告别「在我机器上能跑」的窘境。"
---

## 为什么需要规范化本地开发环境？

在多人协作的项目中，环境不一致是导致「在我机器上能跑」问题的主要根源。Docker Compose 提供了一种声明式的方式来描述整个开发栈，让每位开发者都能快速得到一致的运行环境。

## 项目结构

推荐以下目录结构：

```
project/
├── docker-compose.yml          # 生产环境配置
├── docker-compose.override.yml # 本地开发覆盖配置（不提交）
├── docker-compose.dev.yml      # 开发环境公共配置
├── .env.example                # 环境变量模板
└── services/
    ├── app/
    │   └── Dockerfile
    └── worker/
        └── Dockerfile
```

## 核心配置示例

### docker-compose.yml

```yaml
version: "3.9"

services:
  app:
    build:
      context: .
      dockerfile: services/app/Dockerfile
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=${REDIS_URL}
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    ports:
      - "8000:8000"

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-mydb}
      POSTGRES_USER: ${POSTGRES_USER:-user}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-password}
    volumes:
      - pg_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-user}"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5

volumes:
  pg_data:
```

### docker-compose.dev.yml（开发专用）

```yaml
services:
  app:
    command: uvicorn app.main:app --reload --host 0.0.0.0
    volumes:
      - .:/app  # 挂载源码，支持热重载
    environment:
      - DEBUG=true
    ports:
      - "8000:8000"
      - "5678:5678"  # debugpy 调试端口
```

## 常用技巧

### 1. Healthcheck 让 depends_on 真正生效

Docker Compose v2 以上 `depends_on` 支持 `condition: service_healthy`，可以等待依赖服务真正就绪再启动应用，避免数据库未初始化完成就开始连接的问题。

### 2. 区分开发与生产镜像

使用多阶段构建（multi-stage build）：

```dockerfile
# 基础阶段：安装依赖
FROM python:3.12-slim AS base
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 开发阶段
FROM base AS dev
RUN pip install debugpy pytest
CMD ["python", "-m", "debugpy", "--listen", "0.0.0.0:5678", "-m", "uvicorn", "app.main:app", "--reload"]

# 生产阶段
FROM base AS prod
COPY . .
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--workers", "4"]
```

### 3. `.env` 文件管理

- 提交 `.env.example`，记录所有必须的环境变量
- 将 `.env` 加入 `.gitignore`
- CI/CD 中通过 Secrets 注入敏感变量

### 4. 网络隔离

```yaml
networks:
  frontend:    # 对外暴露
  backend:     # 仅内部通信
    internal: true

services:
  app:
    networks: [frontend, backend]
  db:
    networks: [backend]  # 数据库不暴露到前端网络
```

## 常用命令速查

```bash
# 启动开发环境
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d

# 查看日志
docker compose logs -f app

# 进入容器
docker compose exec app bash

# 重建镜像
docker compose build --no-cache app

# 清理所有容器和数据卷
docker compose down -v
```

## 总结

规范化 Docker Compose 配置的关键点：
1. **健康检查** — 确保服务真正就绪后再启动依赖
2. **环境分离** — 开发/生产使用不同的 override 文件
3. **多阶段构建** — 减小生产镜像体积
4. **网络隔离** — 最小化服务暴露面

一套好的本地开发环境配置，能显著降低团队的上手成本，值得投入时间打磨。
