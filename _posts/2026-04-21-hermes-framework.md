---
title: "Hermes 框架深度解析"
date: 2026-04-21
categories: [人工智能]
tags: [Hermes, AI Agent, LLM, MCP, 本地部署, Qwen, 工具调用, 框架]
excerpt: "全面深入解析 Hermes Agent 编排框架：从代码结构到核心架构，从内置 Skills 到自定义数据库扩展，再到本地 Qwen 环境下的实战使用场景，带你彻底吃透这一生产级 AI Agent 框架。"
---

> 在上一篇《AI Agent》中，我们已经认识了 Hermes 的基本概念。这篇文章将对 Hermes 进行**全面深度解析**：它的代码结构长什么样？有哪些独特的设计理念？内置了哪些开箱即用的能力？如何给它接上自己的数据库？以及在你本地 Qwen 环境下，有哪些好的使用场景？

---

## 一、重新认识 Hermes

### 1.1 它是什么？

**Hermes** 是一个面向**生产环境**的 AI Agent 编排框架。如果说 LangChain 是 Agent 开发的"瑞士军刀"，那么 Hermes 更像一套完整的"军工厂"——它不仅提供工具，还规定了标准化的生产流程、质检体系和安全规范。

> 得名于希腊神话中的**信使之神 Hermes**（赫尔墨斯），他是诸神之间的沟通桥梁，象征着高效、灵活的通信与协作——正是多 Agent 系统最需要的能力。

### 1.2 它解决什么问题？

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph TB
    subgraph 痛点["😰 没有 Hermes 之前的痛点"]
        P1["各 Agent 框架各自为政<br/>代码难以复用"]
        P2["工具接入方式五花八门<br/>维护成本极高"]
        P3["多 Agent 协作靠手写<br/>逻辑复杂易出错"]
        P4["无安全护栏<br/>线上风险大"]
        P5["缺乏可观测性<br/>出了问题不知道为什么"]
    end

    subgraph 方案["✅ Hermes 的解决方案"]
        S1["声明式 Agent 定义<br/>统一配置格式"]
        S2["MCP 协议标准化<br/>工具即插即用"]
        S3["编排引擎内置<br/>工作流可视化"]
        S4["内置 Guardrails<br/>开箱即用安全防护"]
        S5["全链路追踪<br/>每步有据可查"]
    end

    P1 --> S1
    P2 --> S2
    P3 --> S3
    P4 --> S4
    P5 --> S5

    style P1 fill:#ffd6e0,color:#3d3556
    style P2 fill:#ffd6e0,color:#3d3556
    style P3 fill:#ffd6e0,color:#3d3556
    style P4 fill:#ffd6e0,color:#3d3556
    style P5 fill:#ffd6e0,color:#3d3556
    style S1 fill:#b5ead7,color:#3d3556
    style S2 fill:#b5ead7,color:#3d3556
    style S3 fill:#b5ead7,color:#3d3556
    style S4 fill:#b5ead7,color:#3d3556
    style S5 fill:#b5ead7,color:#3d3556
</div>

### 1.3 与其他框架的定位对比

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
quadrantChart
    title AI Agent 框架定位矩阵
    x-axis "灵活 / 低层" --> "开箱即用 / 高层"
    y-axis "单Agent / 简单" --> "多Agent / 复杂"
    quadrant-1 "生产级编排"
    quadrant-2 "全功能但复杂"
    quadrant-3 "学习实验"
    quadrant-4 "快速原型"
    "LangChain": [0.2, 0.35]
    "LlamaIndex": [0.25, 0.25]
    "AutoGen": [0.45, 0.7]
    "CrewAI": [0.6, 0.65]
    "LangGraph": [0.3, 0.75]
    "Hermes": [0.8, 0.85]
</div>

---

## 二、代码结构与项目架构

### 2.1 项目目录结构

安装 `hermes-agent` 后，典型的 Hermes 项目结构如下：

```
my-hermes-project/
│
├── 📁 agents/                    # Agent 定义层
│   ├── __init__.py
│   ├── base_agent.py            # 基础 Agent 类（继承此处扩展）
│   ├── researcher.py            # 示例：研究型 Agent
│   ├── coder.py                 # 示例：编码型 Agent
│   └── reviewer.py              # 示例：审查型 Agent
│
├── 📁 skills/                    # Skills 技能层（内置 + 自定义）
│   ├── __init__.py
│   ├── builtin/                 # 内置 Skills（框架自带）
│   │   ├── web_search.py
│   │   ├── code_executor.py
│   │   ├── file_manager.py
│   │   ├── database_query.py
│   │   └── ...
│   └── custom/                  # 自定义 Skills（你来扩展）
│       └── my_mysql_skill.py
│
├── 📁 config/                    # 配置层
│   ├── agents.yaml              # Agent 声明配置
│   ├── skills.yaml              # Skills 注册配置
│   ├── memory.yaml              # 记忆/数据库配置
│   └── guardrails.yaml          # 安全护栏配置
│
├── 📁 memory/                    # 记忆后端层
│   ├── __init__.py
│   ├── base_memory.py           # 记忆抽象基类
│   ├── chroma_memory.py         # ChromaDB 实现
│   ├── redis_memory.py          # Redis 短期缓存
│   └── custom/                  # 自定义记忆后端
│       └── pg_memory.py
│
├── 📁 workflows/                 # 工作流定义层
│   ├── __init__.py
│   └── pipelines.yaml           # 工作流 DAG 声明
│
├── 📁 guardrails/                # 安全护栏层
│   ├── __init__.py
│   ├── input_validator.py
│   └── output_filter.py
│
├── 📁 observability/             # 可观测性层
│   ├── __init__.py
│   ├── tracer.py                # 链路追踪
│   └── metrics.py               # 性能指标
│
├── docker-compose.yml           # 容器化部署
├── main.py                      # 程序入口
└── requirements.txt
```

### 2.2 整体分层架构

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph TB
    subgraph L0["🖥️ 接入层 Interface Layer"]
        REST["REST API<br/>HTTP/SSE"]
        CLI2["CLI 命令行"]
        SDK["Python SDK"]
        WEB2["Web UI<br/>可视化管理"]
    end

    subgraph L1["🎭 编排层 Orchestration Layer"]
        ORCH["Orchestrator<br/>编排引擎"]
        WF["Workflow Engine<br/>DAG 工作流"]
        SCHED["Scheduler<br/>任务调度"]
    end

    subgraph L2["🤖 Agent 层 Agent Layer"]
        A1["Agent A<br/>（研究）"]
        A2["Agent B<br/>（编码）"]
        A3["Agent C<br/>（审查）"]
        AN["Agent N<br/>（自定义）"]
    end

    subgraph L3["🔧 Skills 层 Skills Layer"]
        SK1["内置 Skills<br/>搜索/代码/文件/数据库"]
        SK2["MCP Server<br/>外部工具服务"]
        SK3["自定义 Skills<br/>用户扩展"]
    end

    subgraph L4["🧠 智能层 Intelligence Layer"]
        LLM2["LLM 接入<br/>OpenAI / Ollama / Qwen"]
        EMB["Embedding<br/>bge-m3 / nomic"]
        RERANK["Reranker<br/>重排序"]
    end

    subgraph L5["💾 记忆层 Memory Layer"]
        STM["短期记忆<br/>Redis / In-Memory"]
        LTM["长期记忆<br/>ChromaDB / Qdrant"]
        EPI["情节记忆<br/>PostgreSQL"]
    end

    subgraph L6["🛡️ 基础设施层 Infrastructure Layer"]
        GR["Guardrails<br/>安全护栏"]
        OBS["Observability<br/>可观测性"]
        AUTH["Auth & RBAC<br/>鉴权"]
    end

    L0 --> L1
    L1 --> L2
    L2 --> L3
    L2 --> L4
    L2 --> L5
    L3 & L4 & L5 --> L6

    style L0 fill:#ffd6e0,color:#3d3556,stroke:#f0a0b0
    style L1 fill:#c7ceea,color:#3d3556,stroke:#a090d8
    style L2 fill:#ffdac1,color:#3d3556,stroke:#ffb380
    style L3 fill:#b5ead7,color:#3d3556,stroke:#81c7b5
    style L4 fill:#e2f0cb,color:#3d3556,stroke:#b0d0a0
    style L5 fill:#bee3db,color:#3d3556,stroke:#80c0b0
    style L6 fill:#d4bbff,color:#3d3556,stroke:#b090e8
</div>

### 2.3 核心模块解析

#### 🎛️ Orchestrator（编排引擎）

编排引擎是 Hermes 的心脏，负责：

- **任务路由**：根据任务类型决定交给哪个 Agent 处理
- **依赖管理**：维护 Agent 间的数据依赖关系（DAG）
- **并发控制**：支持串行、并行、条件分支等多种执行模式
- **错误恢复**：自动重试、降级、告警

```python
# hermes/orchestrator.py（核心模块示意）
from hermes.core import Orchestrator

class Orchestrator:
    def __init__(self, config: OrchestratorConfig):
        self.agents = {}           # 注册的 Agent 字典
        self.skill_registry = {}   # 注册的 Skills 字典
        self.memory_store = None   # 记忆后端
        self.tracer = Tracer()     # 链路追踪器

    async def run(self, workflow: Workflow, inputs: dict) -> dict:
        """执行工作流，返回最终结果"""
        with self.tracer.span("workflow.run"):
            dag = self._build_dag(workflow)
            return await self._execute_dag(dag, inputs)

    def _build_dag(self, workflow: Workflow) -> DAG:
        """将工作流配置转换为可执行的 DAG"""
        ...

    async def _execute_dag(self, dag: DAG, inputs: dict) -> dict:
        """按拓扑顺序执行 DAG 中的每个节点"""
        ...
```

#### 🤖 Agent（智能体基类）

```python
# hermes/agent.py（基类示意）
from hermes.base import BaseAgent

class BaseAgent:
    """所有 Agent 的基类，提供标准生命周期"""

    def __init__(self, name: str, role: str, model: str, skills: list):
        self.name = name
        self.role = role          # 角色描述（注入 System Prompt）
        self.model = model        # 使用的 LLM
        self.skills = skills      # 可用的 Skills 列表
        self.memory = None        # 记忆模块（由 Orchestrator 注入）

    async def think(self, context: str) -> str:
        """核心推理方法：将上下文输入 LLM，获取推理结果"""
        ...

    async def act(self, thought: str) -> ActionResult:
        """根据推理结果执行 Skill 调用"""
        ...

    async def observe(self, result: ActionResult) -> str:
        """观察 Skill 执行结果，决定下一步"""
        ...

    async def run(self, task: str) -> str:
        """ReAct 循环：Think → Act → Observe → ...（直至完成）"""
        ...
```

---

## 三、核心特点与设计理念

### 3.1 六大核心特点

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
mindmap
  root((Hermes<br/>核心特点))
    声明式优先
      YAML 配置 Agent
      代码只写业务逻辑
      配置与代码分离
    MCP 原生支持
      标准化工具协议
      工具即插即用
      跨语言/跨平台
    生产级安全
      输入输出护栏
      PII 检测
      预算/限速控制
    内置可观测性
      全链路追踪
      Prometheus 指标
      Grafana 看板
    多 Agent 协作
      DAG 工作流
      并行执行
      共享记忆
    可扩展记忆
      多后端支持
      短期+长期记忆
      自定义数据库接入
</div>

### 3.2 声明式优先（Configuration-First）

Hermes 最显著的特点之一：**能用配置解决的，绝不写代码**。

```yaml
# config/agents.yaml ——— 这一个文件定义了整个 Agent 系统
orchestrator:
  model: qwen2.5:7b          # ← 本地 Qwen！
  strategy: adaptive          # 自适应编排
  max_iterations: 15

agents:
  analyst:
    name: "数据分析师"
    role: "你是一位资深数据分析师，擅长从数据中发现洞察。"
    model: qwen2.5:7b
    skills:
      - sql_query             # 内置 SQL 查询 Skill
      - data_visualize        # 内置数据可视化 Skill
      - file_read             # 内置文件读取 Skill
    memory:
      type: long_term
      backend: chroma         # 接向量库
    guardrails:
      max_tokens: 4096
      sql_readonly: true      # 只允许 SELECT，拒绝 UPDATE/DELETE

  writer:
    name: "报告撰写员"
    role: "你擅长将复杂分析结果转化为清晰易懂的报告。"
    model: qwen2.5:7b
    skills:
      - file_write
      - markdown_render
```

与 LangChain 的对比：

| 对比维度 | Hermes（声明式） | LangChain（代码式） |
|---------|----------------|-------------------|
| **定义一个 Agent** | 10 行 YAML | 50+ 行 Python |
| **修改工具列表** | 改 YAML 一行 | 找到代码，重新测试 |
| **非技术人员参与** | ✅ 可以 | ❌ 需要开发介入 |
| **版本管理** | Git 管理配置文件 | Git 管理代码 |
| **可读性** | ✅ 极佳 | ⚠️ 因人而异 |

### 3.3 MCP 协议原生支持

MCP（Model Context Protocol）是 Anthropic 提出的**工具通信标准协议**，Hermes 是最早原生支持 MCP 的框架之一。

<div class="mermaid">
sequenceDiagram
    participant Agent as 🤖 Hermes Agent
    participant Reg as 📋 Tool Registry
    participant MCP as 🔌 MCP Server
    participant Tool as 🛠️ 外部工具<br/>（数据库/API/文件系统）

    Agent->>Reg: 我需要"数据库查询"能力
    Reg-->>Agent: 返回对应 MCP Server 地址
    Agent->>MCP: 描述工具(list_tools)
    MCP-->>Agent: 返回可用工具列表 + schema
    Agent->>Agent: LLM 推理：调用 sql_query(sql="SELECT...")
    Agent->>MCP: 调用工具(call_tool)
    MCP->>Tool: 执行 SQL 查询
    Tool-->>MCP: 返回查询结果
    MCP-->>Agent: 格式化结果
    Agent->>Agent: 整合结果，继续推理
</div>

**MCP 的核心优势**：一个 MCP Server 可以被任何支持 MCP 的 Agent 框架使用，真正实现工具的"一次开发，到处运行"。

### 3.4 ReAct + Reflection 双引擎

Hermes 的 Agent 内置两种推理模式，可按任务复杂度自动切换：

<div class="mermaid">
%%{init: {'flowchart': {'rankSpacing': 30, 'nodeSpacing': 15}}}%%
graph LR
    subgraph ReAct["⚡ ReAct 模式（快速任务）"]
        T1["Think<br/>推理"] --> A1["Act<br/>行动"] --> O1["Observe<br/>观察"] --> T1
    end

    subgraph Reflect["🪞 Reflection 模式（复杂任务）"]
        T2["Think<br/>推理"] --> A2["Act<br/>行动"] --> O2["Observe<br/>观察"]
        O2 --> R2["Reflect<br/>反思：结果对吗？"]
        R2 -->|"需要修正"| T2
        R2 -->|"满意"| Done["✅ 完成"]
    end

    Task["📥 任务"] -->|"简单/有限步骤"| ReAct
    Task -->|"复杂/需要自我纠错"| Reflect

    style T1 fill:#b5ead7,color:#3d3556
    style A1 fill:#ffdac1,color:#3d3556
    style O1 fill:#e2f0cb,color:#3d3556
    style T2 fill:#b5ead7,color:#3d3556
    style A2 fill:#ffdac1,color:#3d3556
    style O2 fill:#e2f0cb,color:#3d3556
    style R2 fill:#c7ceea,color:#3d3556
    style Done fill:#bee3db,color:#3d3556
    style Task fill:#ffd6e0,color:#3d3556
</div>

---

## 四、内置 Skills 完整目录

Hermes 开箱即用的 Skills 分为 **六大类**，共覆盖大多数常见 Agent 任务场景。

### 4.1 Skills 全景图

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph TB
    ROOT(["🎯 Hermes<br/>内置 Skills"])

    ROOT --> G1["🌐 Web & 搜索类"]
    ROOT --> G2["💻 代码与执行类"]
    ROOT --> G3["📂 文件与存储类"]
    ROOT --> G4["🗄️ 数据库类"]
    ROOT --> G5["🧠 知识与记忆类"]
    ROOT --> G6["🔗 通信与集成类"]

    G1 --> W1["web_search<br/>网络搜索"]
    G1 --> W2["web_scrape<br/>网页抓取"]
    G1 --> W3["rss_reader<br/>RSS 订阅"]

    G2 --> C1["code_executor<br/>代码沙箱执行"]
    G2 --> C2["shell_runner<br/>Shell 命令执行"]
    G2 --> C3["test_runner<br/>自动化测试"]
    G2 --> C4["code_analyzer<br/>静态代码分析"]

    G3 --> F1["file_read<br/>读取文件"]
    G3 --> F2["file_write<br/>写入文件"]
    G3 --> F3["pdf_parser<br/>PDF 解析"]
    G3 --> F4["image_ocr<br/>图片文字识别"]

    G4 --> D1["sql_query<br/>SQL 数据库查询"]
    G4 --> D2["vector_search<br/>向量相似度检索"]
    G4 --> D3["nosql_query<br/>NoSQL 查询"]

    G5 --> M1["knowledge_retrieve<br/>知识库检索（RAG）"]
    G5 --> M2["memory_save<br/>写入长期记忆"]
    G5 --> M3["memory_recall<br/>召回长期记忆"]

    G6 --> I1["http_request<br/>HTTP API 调用"]
    G6 --> I2["email_send<br/>发送邮件"]
    G6 --> I3["calendar_event<br/>日历操作"]
    G6 --> I4["mcp_call<br/>MCP 通用调用"]

    style ROOT fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style G1 fill:#b5ead7,color:#3d3556
    style G2 fill:#ffdac1,color:#3d3556
    style G3 fill:#ffd6e0,color:#3d3556
    style G4 fill:#e2f0cb,color:#3d3556
    style G5 fill:#bee3db,color:#3d3556
    style G6 fill:#d4bbff,color:#3d3556
</div>

### 4.2 重点 Skills 详解

#### 🌐 web_search（网络搜索）

```python
# 在 Agent 中使用 web_search
from hermes import Agent, use_skill

class ResearchAgent(Agent):

    @use_skill("web_search")
    async def search_latest_news(self, query: str) -> list[dict]:
        """
        搜索最新信息，返回结果列表
        每条结果包含: title, url, snippet, published_at
        """
        # Hermes 自动注入工具执行逻辑，这里只需定义接口
        pass
```

```yaml
# config/skills.yaml 中的配置
skills:
  web_search:
    provider: duckduckgo      # 可选: google, bing, duckduckgo, serper
    max_results: 5
    safe_search: moderate
    timeout: 10
```

#### 💻 code_executor（代码沙箱）

Hermes 的代码执行环境是**完全隔离的沙箱**，不会影响宿主机：

```yaml
skills:
  code_executor:
    backend: docker           # 使用 Docker 容器隔离
    image: "python:3.11-slim"
    timeout: 30               # 最长执行 30 秒
    memory_limit: "256m"
    network: none             # 禁止网络访问（安全）
    allowed_packages:
      - numpy
      - pandas
      - matplotlib
      - scipy
```

```python
# Agent 使用代码执行器
result = await agent.run_skill("code_executor", code="""
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('/workspace/data.csv')
summary = df.describe()
print(summary.to_string())
""")
print(result.stdout)   # 代码输出
print(result.files)    # 生成的文件（如图表）
```

#### 🗄️ sql_query（SQL 查询）

```python
# 内置 SQL 查询 Skill，支持多种数据库
result = await agent.run_skill("sql_query",
    connection="mysql://user:pass@localhost/mydb",
    sql="SELECT * FROM orders WHERE date >= '2026-01-01' LIMIT 100"
)
# result.rows 包含查询结果
# result.columns 包含列名
```

#### 🧠 knowledge_retrieve（RAG 检索）

```python
# 从知识库中检索相关内容
chunks = await agent.run_skill("knowledge_retrieve",
    collection="company_docs",   # 知识库集合名
    query="季度财报审计流程",
    top_k=5,
    rerank=True                  # 启用重排序提升精度
)
for chunk in chunks:
    print(f"[{chunk.score:.3f}] {chunk.text[:100]}...")
```

### 4.3 Skills 使用方式汇总

| 使用方式 | 场景 | 示例 |
|---------|------|------|
| **配置文件声明** | 在 agents.yaml 中给 Agent 分配 Skills | `skills: [web_search, code_executor]` |
| **装饰器注入** | 在 Agent 类方法上使用 `@use_skill` | `@use_skill("sql_query")` |
| **直接调用** | 工作流中某个步骤执行特定 Skill | `await agent.run_skill("file_write", ...)` |
| **LLM 自主选择** | Agent 根据任务自动决定调用哪个 Skill | 无需手动指定，LLM 推理后调用 |

---

## 五、扩展自定义数据库

这是很多人最关心的问题：**如何把自己的数据库（MySQL、Elasticsearch、MongoDB……）接入 Hermes？**

Hermes 提供了两种扩展方式，适合不同场景。

### 5.1 两种扩展路径

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph TB
    Need["我想让 Hermes<br/>使用我的数据库"]

    Need --> Way1["方式一：自定义 Memory Backend<br/>（用于长期记忆/知识库）"]
    Need --> Way2["方式二：自定义 Skill<br/>（用于直接查询/写入操作）"]

    Way1 --> W1A["适合：文档向量检索<br/>语义搜索、知识库存储"]
    Way1 --> W1B["实现：继承 BaseMemory<br/>实现 save/retrieve 接口"]

    Way2 --> W2A["适合：精确查询、数据写入<br/>报表生成、业务逻辑"]
    Way2 --> W2B["实现：继承 BaseSkill<br/>实现 execute 接口"]

    style Need fill:#ffd6e0,color:#3d3556
    style Way1 fill:#b5ead7,color:#3d3556
    style Way2 fill:#ffdac1,color:#3d3556
    style W1A fill:#bee3db,color:#3d3556
    style W1B fill:#bee3db,color:#3d3556
    style W2A fill:#e2f0cb,color:#3d3556
    style W2B fill:#e2f0cb,color:#3d3556
</div>

### 5.2 方式一：自定义 Memory Backend（接向量检索数据库）

适合场景：你有 Elasticsearch、Milvus、自建 PostgreSQL+pgvector，想让 Hermes Agent 检索其中的知识。

#### 第一步：继承 BaseMemory

```python
# memory/custom/elasticsearch_memory.py
from hermes.memory import BaseMemory, MemoryEntry, SearchResult
from elasticsearch import AsyncElasticsearch
from typing import List, Optional

class ElasticsearchMemory(BaseMemory):
    """
    将 Elasticsearch 接入 Hermes 作为长期记忆后端
    支持全文检索 + 向量检索（kNN）
    """

    def __init__(self, config: dict):
        super().__init__(config)
        self.es = AsyncElasticsearch(
            hosts=[config["host"]],
            api_key=config.get("api_key")
        )
        self.index = config.get("index", "hermes-memory")
        self.embed_model = config.get("embed_model", "bge-m3")

    async def save(self, entry: MemoryEntry) -> str:
        """将一条记忆写入 Elasticsearch"""
        # 1. 将内容转为向量（调用本地 Embedding 模型）
        vector = await self._embed(entry.content)

        # 2. 写入 ES（同时存储原文和向量）
        doc = {
            "agent_id": entry.agent_id,
            "content": entry.content,
            "metadata": entry.metadata,
            "vector": vector,
            "created_at": entry.created_at.isoformat(),
        }
        result = await self.es.index(
            index=self.index,
            document=doc
        )
        return result["_id"]

    async def retrieve(
        self,
        query: str,
        agent_id: Optional[str] = None,
        top_k: int = 5
    ) -> List[SearchResult]:
        """从 Elasticsearch 语义检索相关记忆"""
        # 1. 查询向量化
        query_vector = await self._embed(query)

        # 2. kNN 向量检索
        knn_query = {
            "knn": {
                "field": "vector",
                "query_vector": query_vector,
                "k": top_k,
                "num_candidates": 50,
            }
        }
        if agent_id:
            knn_query["filter"] = {"term": {"agent_id": agent_id}}

        response = await self.es.search(
            index=self.index,
            body=knn_query
        )

        # 3. 转换为 Hermes 标准 SearchResult 格式
        results = []
        for hit in response["hits"]["hits"]:
            results.append(SearchResult(
                id=hit["_id"],
                content=hit["_source"]["content"],
                score=hit["_score"],
                metadata=hit["_source"]["metadata"]
            ))
        return results

    async def _embed(self, text: str) -> List[float]:
        """调用本地 Ollama bge-m3 生成向量"""
        import httpx
        async with httpx.AsyncClient() as client:
            resp = await client.post(
                "http://localhost:11434/api/embeddings",
                json={"model": self.embed_model, "prompt": text}
            )
            return resp.json()["embedding"]
```

#### 第二步：注册到配置文件

```yaml
# config/memory.yaml
memory_backends:
  # 注册自定义后端
  elasticsearch:
    class: "memory.custom.elasticsearch_memory.ElasticsearchMemory"
    host: "http://localhost:9200"
    api_key: "${ES_API_KEY}"          # 从环境变量读取
    index: "my-company-knowledge"
    embed_model: "bge-m3"

  # 还可以同时注册多个后端
  chroma_local:
    class: "hermes.memory.ChromaMemory"   # 内置后端
    path: "./data/chroma"

# 不同 Agent 使用不同后端
agents:
  analyst:
    memory:
      type: long_term
      backend: elasticsearch       # 使用你的 ES 后端

  writer:
    memory:
      type: long_term
      backend: chroma_local        # 使用本地 Chroma
```

#### 第三步：测试与验证

```python
# test_es_memory.py
import asyncio
from hermes import Orchestrator
from hermes.config import load_config

async def test():
    config = load_config("config/agents.yaml")
    orchestrator = Orchestrator(config)

    # 手动写入一条记忆
    analyst = orchestrator.get_agent("analyst")
    await analyst.memory.save({
        "content": "2026年Q1销售额为 3200 万，同比增长 15%",
        "metadata": {"source": "finance_report", "year": 2026}
    })

    # 语义检索
    results = await analyst.memory.retrieve("第一季度销售情况")
    for r in results:
        print(f"[{r.score:.3f}] {r.content}")

asyncio.run(test())
```

### 5.3 方式二：自定义 Skill（接任意数据库直接查询）

适合场景：你想让 Agent 直接查询 MySQL 业务数据库、Redis 缓存、MongoDB 文档库。

#### 扩展 MySQL 查询 Skill

```python
# skills/custom/mysql_skill.py
from hermes.skills import BaseSkill, SkillResult
from hermes.skills.decorators import skill_meta
import aiomysql
import json

@skill_meta(
    name="mysql_business_query",
    description="查询公司 MySQL 业务数据库，支持订单、用户、产品等数据查询",
    # 以下 schema 会被注入到 LLM 的工具列表，让 LLM 知道如何调用
    parameters={
        "type": "object",
        "properties": {
            "query_type": {
                "type": "string",
                "enum": ["orders", "users", "products", "revenue"],
                "description": "要查询的数据类型"
            },
            "filters": {
                "type": "object",
                "description": "过滤条件，如 {date_from: '2026-01-01', status: 'completed'}"
            },
            "limit": {
                "type": "integer",
                "default": 100,
                "description": "返回记录数量上限"
            }
        },
        "required": ["query_type"]
    }
)
class MySQLBusinessSkill(BaseSkill):

    # 每种 query_type 对应的 SQL 模板（防止 SQL 注入）
    QUERY_TEMPLATES = {
        "orders": """
            SELECT order_id, user_id, amount, status, created_at
            FROM orders
            WHERE 1=1
            {date_filter}
            {status_filter}
            ORDER BY created_at DESC
            LIMIT {limit}
        """,
        "revenue": """
            SELECT
                DATE_FORMAT(created_at, '%Y-%m') AS month,
                SUM(amount) AS total_revenue,
                COUNT(*) AS order_count
            FROM orders
            WHERE status = 'completed'
            {date_filter}
            GROUP BY month
            ORDER BY month DESC
            LIMIT {limit}
        """,
        # ... 其他模板
    }

    async def setup(self):
        """Skill 初始化时建立数据库连接池"""
        self.pool = await aiomysql.create_pool(
            host=self.config["host"],
            port=self.config.get("port", 3306),
            user=self.config["user"],
            password=self.config["password"],
            db=self.config["database"],
            minsize=1,
            maxsize=5,
            autocommit=True
        )

    async def execute(self, query_type: str, filters: dict = None, limit: int = 100) -> SkillResult:
        """执行查询，返回标准格式结果"""
        filters = filters or {}

        # 1. 根据 query_type 选择 SQL 模板（避免 LLM 直接拼 SQL）
        template = self.QUERY_TEMPLATES.get(query_type)
        if not template:
            return SkillResult.error(f"不支持的查询类型: {query_type}")

        # 2. 安全地填充过滤条件
        date_filter = ""
        if "date_from" in filters:
            date_filter += f" AND created_at >= '{filters['date_from']}'"
        if "date_to" in filters:
            date_filter += f" AND created_at <= '{filters['date_to']}'"

        status_filter = ""
        if "status" in filters:
            status_filter = f" AND status = '{filters['status']}'"

        sql = template.format(
            date_filter=date_filter,
            status_filter=status_filter,
            limit=min(limit, 1000)     # 最多返回 1000 条（安全限制）
        )

        # 3. 执行查询
        async with self.pool.acquire() as conn:
            async with conn.cursor(aiomysql.DictCursor) as cur:
                await cur.execute(sql)
                rows = await cur.fetchall()

        # 4. 返回标准格式
        return SkillResult.success(
            data=rows,
            summary=f"查询 {query_type}，返回 {len(rows)} 条记录",
            metadata={"query_type": query_type, "row_count": len(rows)}
        )

    async def teardown(self):
        """关闭连接池"""
        self.pool.close()
        await self.pool.wait_closed()
```

#### 注册自定义 Skill

```yaml
# config/skills.yaml
custom_skills:
  mysql_business_query:
    class: "skills.custom.mysql_skill.MySQLBusinessSkill"
    host: "${MYSQL_HOST}"
    user: "${MYSQL_USER}"
    password: "${MYSQL_PASSWORD}"
    database: "business_db"

# 在 Agent 配置中使用
agents:
  data_analyst:
    skills:
      - mysql_business_query     # 你的自定义 Skill
      - data_visualize           # 内置 Skill
      - file_write               # 内置 Skill
```

### 5.4 扩展其他数据库（快速参考）

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph LR
    Hermes["🎯 Hermes<br/>Agent"] --> I1["📐 BaseSkill<br/>扩展接口"]
    Hermes --> I2["🧠 BaseMemory<br/>扩展接口"]

    I1 --> DB1["🐬 MySQL<br/>业务数据库"]
    I1 --> DB2["🐘 PostgreSQL<br/>关系型数据"]
    I1 --> DB3["🍃 MongoDB<br/>文档数据库"]
    I1 --> DB4["🔴 Redis<br/>缓存/队列"]
    I1 --> DB5["🔍 Elasticsearch<br/>全文搜索"]
    I1 --> DB6["📊 ClickHouse<br/>OLAP 分析"]

    I2 --> M1["🗄️ Chroma<br/>本地向量库（内置）"]
    I2 --> M2["⚡ Qdrant<br/>生产向量库（内置）"]
    I2 --> M3["🔍 Elasticsearch<br/>向量+全文（自定义）"]
    I2 --> M4["🐘 pgvector<br/>PostgreSQL 插件（自定义）"]
    I2 --> M5["🏗️ Milvus<br/>企业向量库（自定义）"]

    style Hermes fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style I1 fill:#ffdac1,color:#3d3556
    style I2 fill:#b5ead7,color:#3d3556
</div>

| 数据库 | 扩展方式 | 难度 | 典型用途 |
|--------|---------|------|---------|
| **MySQL / PostgreSQL** | 自定义 Skill | ⭐ 简单 | 业务数据查询、报表生成 |
| **MongoDB** | 自定义 Skill | ⭐ 简单 | 文档检索、日志分析 |
| **Redis** | 自定义 Skill | ⭐ 简单 | 实时数据、缓存查询 |
| **Elasticsearch** | Memory Backend | ⭐⭐ 中等 | 全文+语义混合检索 |
| **pgvector** | Memory Backend | ⭐⭐ 中等 | 利用已有 PG 做向量检索 |
| **Milvus** | Memory Backend | ⭐⭐⭐ 较难 | 亿级向量数据企业场景 |
| **ClickHouse** | 自定义 Skill | ⭐⭐ 中等 | 大规模 OLAP 分析 |

---

## 六、本地安装后的最佳使用场景

你已经有了本地 Qwen + Ollama 环境。下面给出 **5 个最适合本地部署 Hermes 的使用场景**，从简单到复杂排列。

### 6.1 场景概览

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph LR
    subgraph 你的本地环境["💻 你的本地环境"]
        Qwen2["🤖 Qwen 模型<br/>Ollama 服务"]
        Hermes2["🚀 Hermes 框架"]
        Chroma2["🗄️ ChromaDB<br/>向量存储"]
    end

    Hermes2 --> S1["📖 场景一<br/>私人文档问答助手"]
    Hermes2 --> S2["💻 场景二<br/>代码审查与重构助手"]
    Hermes2 --> S3["📊 场景三<br/>本地数据分析 Agent"]
    Hermes2 --> S4["🔬 场景四<br/>多文档研究助理"]
    Hermes2 --> S5["🤖 场景五<br/>自动化任务流水线"]

    style Qwen2 fill:#ffd6e0,color:#3d3556
    style Hermes2 fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style Chroma2 fill:#b5ead7,color:#3d3556
    style S1 fill:#ffdac1,color:#3d3556
    style S2 fill:#ffdac1,color:#3d3556
    style S3 fill:#ffdac1,color:#3d3556
    style S4 fill:#ffdac1,color:#3d3556
    style S5 fill:#ffdac1,color:#3d3556
</div>

### 6.2 场景一：私人文档问答助手 ⭐（推荐首选）

**需求**：把你的技术笔记、PDF 手册、Word 文档全部"喂"给 Agent，随时用自然语言提问。

**特别适合**：
- 存了几百个 PDF 但找起来很困难的人
- 需要频繁查阅技术文档或规范的开发者
- 记笔记但很少回顾的人

```yaml
# config/agents.yaml
agents:
  personal_assistant:
    name: "私人知识助手"
    role: "你是一个私人知识管理助手，帮助用户从他的个人文档库中检索信息并给出答案。回答时请引用具体的文档来源。"
    model: qwen2.5:14b          # 14B 理解复杂文档更准确
    skills:
      - knowledge_retrieve       # 核心：RAG 检索
      - file_read                # 读取文档
    memory:
      type: long_term
      backend: chroma
      collection: "personal_docs"
```

```bash
# 第一步：把你的文档索引到 ChromaDB
python -m hermes index \
  --source ~/Documents/notes \
  --source ~/Downloads/books \
  --include "*.pdf,*.md,*.txt,*.docx" \
  --collection personal_docs \
  --embed-model bge-m3

# 索引完成后直接开始对话
python -m hermes chat --agent personal_assistant
```

```
❓ 你: LangChain 和 LlamaIndex 在 RAG 场景下的主要区别是什么？

💬 助手: 根据你的文档《AI框架对比.md》中的内容：

  LangChain 是通用 AI 应用框架，RAG 是其众多能力之一，适合需要
  将 RAG 与其他 Agent 能力（工具调用、记忆管理等）结合使用的场景。

  LlamaIndex 专为 RAG 设计，在文档解析、索引策略、检索优化方面
  提供了更丰富的内置能力（如 HyDE、多查询、重排序等）...

  📎 来源: AI框架对比.md (第3节), RAG技术总结.pdf (第12页)
```

### 6.3 场景二：代码审查与重构助手 ⭐⭐

**需求**：让 Agent 自动审查代码质量、发现潜在 Bug、提出重构建议，并能直接执行修改。

```yaml
agents:
  code_reviewer:
    name: "代码审查专家"
    role: |
      你是一位经验丰富的高级工程师，专注于代码质量审查。
      你擅长发现：安全漏洞、性能问题、设计模式违反、
      可读性问题和潜在 Bug。给出建议时请附上具体的修改示例。
    model: qwen2.5:14b
    skills:
      - file_read                # 读取代码文件
      - code_analyzer            # 静态分析（复杂度/依赖）
      - knowledge_retrieve       # 检索编码规范知识库
      - file_write               # 写入修改后的文件

workflows:
  code_review:
    steps:
      - agent: code_reviewer
        task: "审查以下文件的代码质量并给出详细报告：{file_path}"
        output: review_report
      - agent: code_reviewer
        task: "根据审查报告，对代码进行必要的重构（保留原文件，生成 _refactored 版本）"
        input: review_report
```

```bash
# 审查单个文件
python -m hermes run code_review \
  --file src/utils/database.py

# 批量审查整个目录
python -m hermes run code_review \
  --dir src/ \
  --include "*.py"
```

### 6.4 场景三：本地数据分析 Agent ⭐⭐

**需求**：上传 CSV/Excel 数据文件，用自然语言描述分析需求，Agent 自动生成分析代码、执行并输出可视化报告。

```yaml
agents:
  data_analyst:
    name: "数据分析师"
    role: |
      你是一位资深数据分析师，擅长使用 Python（pandas, matplotlib, seaborn）
      进行数据分析和可视化。收到分析需求后，你会：
      1. 先读取数据，了解数据结构
      2. 编写分析代码
      3. 执行代码，生成图表
      4. 撰写文字分析结论
    model: qwen2.5:14b
    skills:
      - file_read
      - code_executor            # 沙箱执行 Python 代码
      - file_write               # 写出报告
```

```bash
# 启动数据分析对话
python -m hermes chat --agent data_analyst

# 示例对话：
# 你: 我上传了 sales_2026.csv，帮我分析各地区的月度销售趋势，
#     找出增长最快的地区，并生成折线图
#
# Agent: 好的，我先读取数据了解结构...
#        [读取文件]
#        数据包含 12 个月、8 个地区的销售数据。
#        [生成并执行分析代码]
#        [生成 sales_trend.png]
#        分析结论：华东区增长最快（+34%），其次是...
```

### 6.5 场景四：多文档研究助理 ⭐⭐⭐

**需求**：给定一个研究主题，Agent 自动搜索网络、阅读本地参考资料、综合分析并输出结构化研究报告。

<div class="mermaid">
sequenceDiagram
    participant You as 👨‍💻 你
    participant Orch as 🎭 Orchestrator
    participant Rsrch as 🔍 研究 Agent
    participant Write as ✍️ 写作 Agent
    participant KB5 as 📚 本地知识库
    participant Net as 🌐 网络搜索

    You->>Orch: "研究：2026年大模型推理加速技术现状"
    Orch->>Rsrch: 分配研究任务
    Rsrch->>KB5: 检索本地已有资料
    KB5-->>Rsrch: 返回相关文档片段
    Rsrch->>Net: 搜索最新进展
    Net-->>Rsrch: 返回最新信息
    Rsrch->>Rsrch: 综合分析，整理要点
    Rsrch->>Orch: 返回研究素材
    Orch->>Write: 分配写作任务 + 研究素材
    Write->>Write: 撰写结构化报告
    Write->>You: 输出 Markdown 报告 + 参考文献
</div>

```yaml
agents:
  researcher:
    name: "研究员"
    skills: [web_search, knowledge_retrieve, file_read]
    model: qwen2.5:14b

  report_writer:
    name: "报告撰写员"
    role: "基于研究材料，撰写结构清晰、引用准确的技术研究报告。"
    skills: [file_write, markdown_render]
    model: qwen2.5:14b

workflows:
  research_report:
    steps:
      - agent: researcher
        task: "研究主题：{topic}。综合搜索网络信息和本地知识库，整理关键发现。"
        output: research_notes
      - agent: report_writer
        task: "基于研究笔记，撰写完整的研究报告：{research_notes}"
        output_file: "reports/{topic}.md"
```

```bash
python -m hermes run research_report \
  --topic "2026年大模型推理加速技术现状"
# 几分钟后，reports/ 目录下会生成完整的 Markdown 研究报告
```

### 6.6 场景五：自动化任务流水线 ⭐⭐⭐

**需求**：把重复的日常工作（如每日数据报告、代码提交后的自动测试+文档更新）自动化。

```yaml
# 每日数据报告工作流
workflows:
  daily_report:
    schedule: "0 9 * * 1-5"           # 工作日早9点自动触发
    steps:
      - agent: data_analyst
        task: "从 MySQL 查询昨日业务数据，生成日报摘要"
        output: data_summary
      - agent: report_writer
        task: "将数据摘要整理为日报邮件格式：{data_summary}"
        output: email_content
      - skill: email_send
        params:
          to: ["team@company.com"]
          subject: "每日业务数据报告 - {today}"
          body: "{email_content}"
```

```bash
# 立即执行一次（测试）
python -m hermes workflow run daily_report --now

# 启动后台调度服务（持续运行）
python -m hermes scheduler start
```

### 6.7 场景能力对比

| 场景 | 依赖模型大小 | 需要联网 | 实现难度 | 价值评级 |
|------|------------|---------|---------|---------|
| **私人文档助手** | 7B 即可 | ❌ 不需要 | ⭐ 简单 | ⭐⭐⭐⭐⭐ 极高 |
| **代码审查助手** | 14B 更佳 | ❌ 不需要 | ⭐⭐ 中等 | ⭐⭐⭐⭐ 高 |
| **本地数据分析** | 14B 更佳 | ❌ 不需要 | ⭐⭐ 中等 | ⭐⭐⭐⭐ 高 |
| **多文档研究** | 14B 推荐 | ✅ 需要 | ⭐⭐⭐ 较高 | ⭐⭐⭐⭐ 高 |
| **自动化流水线** | 7B–14B | 按需 | ⭐⭐⭐ 较高 | ⭐⭐⭐⭐⭐ 极高 |

---

## 七、本地环境快速上手指南

### 7.1 你的本地配置建议

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph TB
    subgraph 你已有的["✅ 你已有的环境"]
        Q["🤖 Qwen 模型<br/>via Ollama"]
        VS["🖊️ VSCode + Copilot"]
    end

    subgraph 需要补充的["🔧 需要补充安装的"]
        H["🚀 hermes-agent<br/>pip install hermes-agent"]
        C["🗄️ ChromaDB<br/>pip install chromadb"]
        B["🔢 bge-m3 Embedding<br/>ollama pull bge-m3"]
    end

    subgraph 可选的["⚙️ 可选增强"]
        R["🔴 Redis<br/>短期记忆缓存"]
        P["📊 Prometheus+Grafana<br/>可观测性监控"]
    end

    Q & VS -->|"已就绪"| H
    H --> C
    H --> B
    H -->|"进阶"| R
    H -->|"进阶"| P

    style Q fill:#b5ead7,color:#3d3556
    style VS fill:#b5ead7,color:#3d3556
    style H fill:#ffdac1,color:#3d3556,stroke:#ffb380,stroke-width:2px
    style C fill:#e2f0cb,color:#3d3556
    style B fill:#e2f0cb,color:#3d3556
    style R fill:#ffd6e0,color:#3d3556
    style P fill:#ffd6e0,color:#3d3556
</div>

### 7.2 最简上手（15 分钟版）

```bash
# ① 安装 Hermes 和依赖
pip install hermes-agent chromadb

# ② 拉取 Embedding 模型（如果还没有）
ollama pull bge-m3

# ③ 创建你的第一个 Hermes 项目
hermes init my-assistant
cd my-assistant

# ④ 修改 config/agents.yaml，把模型改成本地 Qwen
# 打开文件，将 model: gpt-4o 改为 model: qwen2.5:7b
# 将 api_base: https://api.openai.com/v1 改为 api_base: http://localhost:11434/v1

# ⑤ 索引你的文档
hermes index --source ~/Documents --collection my_docs

# ⑥ 开始对话！
hermes chat --agent personal_assistant
```

### 7.3 配置本地 Qwen 作为后端

```yaml
# config/agents.yaml ——— 本地 Qwen 专用配置
llm:
  provider: ollama
  base_url: "http://localhost:11434/v1"
  default_model: qwen2.5:7b       # 低显存机器用 7B
  # default_model: qwen2.5:14b    # 高显存机器推荐 14B
  temperature: 0.1                 # 任务型 Agent 低温度更稳定
  timeout: 120                     # 本地推理可能较慢，适当增大超时

embedding:
  provider: ollama
  base_url: "http://localhost:11434"
  model: bge-m3                    # 中文场景必选

memory:
  default_backend: chroma
  chroma:
    path: "./data/chroma"          # 向量数据持久化路径
```

---

## 八、常见问题与进阶技巧

### 8.1 常见问题排查

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| **Agent 回答超时** | 本地模型推理慢 | 增大 `timeout` 参数，或换更小模型 |
| **检索结果不准** | Embedding 模型不匹配 | 中文必须使用 bge-m3 或 bge-large-zh |
| **Skill 调用失败** | MCP Server 未启动 | 检查 `hermes doctor` 输出 |
| **内存不足** | 模型太大 + ChromaDB 占用 | 使用 `qwen2.5:7b`，限制 ChromaDB 缓存 |
| **Agent 陷入循环** | 任务描述不清晰 | 增加 `max_iterations` 限制，优化 role 提示 |
| **中文乱码** | 文档编码问题 | 确保文档 UTF-8 编码，使用 `hermes index --encoding utf-8` |

### 8.2 性能优化技巧

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph LR
    subgraph 模型层["🤖 模型层优化"]
        M1["量化模型<br/>q4_K_M 版本<br/>速度↑ 质量≈"]
        M2["合理分配角色<br/>简单任务用7B<br/>复杂任务用14B"]
    end

    subgraph 检索层["🔍 检索层优化"]
        R1["调整 chunk_size<br/>中文推荐 256-512"]
        R2["启用 Reranker<br/>精度显著提升"]
        R3["混合检索<br/>向量+BM25"]
    end

    subgraph 缓存层["⚡ 缓存层优化"]
        C1["启用 Redis<br/>缓存高频 Skill 结果"]
        C2["Embedding 缓存<br/>避免重复向量化"]
    end

    style M1 fill:#b5ead7,color:#3d3556
    style M2 fill:#b5ead7,color:#3d3556
    style R1 fill:#ffdac1,color:#3d3556
    style R2 fill:#ffdac1,color:#3d3556
    style R3 fill:#ffdac1,color:#3d3556
    style C1 fill:#c7ceea,color:#3d3556
    style C2 fill:#c7ceea,color:#3d3556
</div>

### 8.3 从入门到进阶的成长路径

<div class="mermaid">
graph LR
    L1["🌱 入门<br/>文档助手<br/>单 Agent + RAG"] -->|掌握基础| L2["🌿 进阶<br/>数据分析<br/>+ 代码执行 Skill"]
    L2 -->|多 Agent 协作| L3["🌳 高级<br/>研究流水线<br/>多 Agent 工作流"]
    L3 -->|自定义扩展| L4["🏆 专家<br/>定制化企业级<br/>自定义 DB + Skill"]

    style L1 fill:#b5ead7,color:#3d3556
    style L2 fill:#ffdac1,color:#3d3556
    style L3 fill:#c7ceea,color:#3d3556
    style L4 fill:#ffd6e0,color:#3d3556,stroke:#f0a0b0,stroke-width:2px
</div>

---

## 九、总结

### 9.1 Hermes 核心要点一览

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph TB
    Core(["🚀 Hermes<br/>核心要点"])

    Core --> T1["🏗️ 代码结构<br/>六层架构<br/>接入→编排→Agent<br/>→Skills→智能→记忆"]
    Core --> T2["✨ 核心特点<br/>声明式配置<br/>MCP原生支持<br/>内置护栏+可观测"]
    Core --> T3["🎯 内置Skills<br/>六大类30+工具<br/>搜索/代码/文件<br/>数据库/知识/通信"]
    Core --> T4["🔌 数据库扩展<br/>BaseSkill精确查询<br/>BaseMemory向量检索<br/>两种路径按需选择"]
    Core --> T5["💡 本地场景<br/>文档助手⭐首选<br/>代码审查/数据分析<br/>研究助理/自动化"]

    style Core fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style T1 fill:#ffd6e0,color:#3d3556
    style T2 fill:#b5ead7,color:#3d3556
    style T3 fill:#ffdac1,color:#3d3556
    style T4 fill:#e2f0cb,color:#3d3556
    style T5 fill:#bee3db,color:#3d3556,stroke:#81c7b5,stroke-width:2px
</div>

### 9.2 你的下一步行动

1. **今天**：`pip install hermes-agent`，运行 `hermes init` 创建第一个项目，感受配置文件的简洁
2. **这周**：用**场景一（文档助手）**，把你最常查阅的技术文档索引进去，体验语义搜索的魔力
3. **下周**：尝试**场景三（数据分析）**，上传一个你有的 CSV 文件，用自然语言做一次完整分析
4. **一个月内**：如果你有 MySQL 或其他业务数据库，参照第五章扩展一个自定义 Skill，实现真正的"和你的数据对话"

> **一句话总结**：Hermes 把"打造生产级 AI Agent"这件本来需要数周的工程工作，压缩到了几天内可以完成的事——而你已经有了最关键的一块：本地 Qwen 模型。现在缺的只是把它们连接起来。

---

*本文持续更新，欢迎在 Issues 中提问或补充。*
