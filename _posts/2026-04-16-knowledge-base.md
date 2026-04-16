---
title: "知识库"
date: 2026-04-16
categories: [人工智能]
tags: [知识库, RAG, Agent, 向量数据库, Qwen, 本地部署]
excerpt: "从零认识 Agent 知识库：涵盖知识库类型、核心技术（Embedding / 向量检索 / RAG）、主流搭建方案对比，以及基于本地 Qwen + VSCode + Copilot 的实战搭建全流程。"
---

## 一、什么是知识库

### 1.1 从"记忆"说起

想象你雇了一位新员工，他聪明能干，但对公司业务一无所知。每次你问他公司规章制度、产品手册，他都茫然摇头。**AI Agent 也面临同样的问题** —— 大模型（LLM）训练完成后知识就固化了，它不了解你的私有文档、最新资讯，也无法记住超出上下文窗口的内容。

**知识库（Knowledge Base）** 正是解决这一问题的核心组件：它是 Agent 的"外挂大脑"，让 Agent 能够按需检索、补充自己不具备的知识。

### 1.2 传统知识库 vs Agent 知识库

| 维度 | 传统知识库 | Agent 知识库 |
|------|-----------|-------------|
| **形式** | FAQ 问答对、结构化数据库 | 文档、代码、音视频、图表等多模态 |
| **检索方式** | 关键词精确匹配 | 语义向量检索 + 混合检索 |
| **更新方式** | 人工维护 | 自动化 ETL 流水线 |
| **使用方式** | 人工查询 | Agent 自主调用（RAG 模式） |
| **智能程度** | 静态规则 | 结合 LLM 动态生成答案 |
| **典型场景** | 客服 FAQ | 智能文档问答、代码助手、研究助理 |

### 1.3 知识库在 Agent 架构中的位置

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '16px'}}}%%
graph TB
    User[👤 用户提问] --> Brain[🧠 LLM 大脑<br/>Qwen / GPT 等]
    Brain --> Planner[📋 规划器<br/>任务分解]
    Planner --> KB[📚 知识库检索<br/>RAG 召回]
    Planner --> Tools[🔧 工具调用<br/>搜索 / 代码 / API]
    KB --> Retriever[🔍 检索引擎<br/>向量 + 关键词]
    Retriever --> VecDB[(🗄️ 向量数据库<br/>Chroma / Milvus)]
    Retriever --> DocStore[(📂 文档存储<br/>原始文件)]
    KB --> Brain
    Tools --> Brain
    Brain --> Output[💬 最终回答]

    style User fill:#ffd6e0,color:#3d3556
    style Brain fill:#c7ceea,color:#3d3556
    style Planner fill:#ffdac1,color:#3d3556
    style KB fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style Tools fill:#ffdac1,color:#3d3556
    style Retriever fill:#e2f0cb,color:#3d3556
    style VecDB fill:#bee3db,color:#3d3556
    style DocStore fill:#bee3db,color:#3d3556
    style Output fill:#ffd6e0,color:#3d3556
</div>

> **核心思想**：用户提问 → LLM 判断需要哪些背景知识 → 从知识库检索相关片段 → 将片段注入到 Prompt → LLM 生成有依据的回答。这就是 **RAG（Retrieval-Augmented Generation，检索增强生成）** 模式。

---

## 二、知识库的类型

知识库并非只有一种形态。根据数据组织方式和使用场景，可以分为以下四大类：

### 2.1 全景概览

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
mindmap
  root((知识库类型))
    结构化知识库
      关系型数据库
      表格/Excel
      JSON/XML
    向量知识库
      文档知识库
      代码知识库
      多模态知识库
    图谱知识库
      实体关系图谱
      知识图谱 KG
      本体论 Ontology
    混合知识库
      向量 + 图谱
      向量 + 关键词
      多模态融合
</div>

### 2.2 结构化知识库

数据以固定格式（行列、JSON 等）存储，通过 SQL 或精确查询检索。

<div class="mermaid">
graph LR
    Q["用户问：<br/>Q3销售额是多少？"] --> NL2SQL["NL→SQL<br/>转换器"]
    NL2SQL --> DB[("💾 关系型数据库<br/>MySQL / PostgreSQL")]
    DB --> R["SELECT SUM(amount)<br/>FROM orders<br/>WHERE quarter='Q3'"]
    R --> LLM["🧠 LLM 生成<br/>自然语言回答"]

    style Q fill:#ffd6e0,color:#3d3556
    style NL2SQL fill:#c7ceea,color:#3d3556
    style DB fill:#b5ead7,color:#3d3556
    style R fill:#e2f0cb,color:#3d3556
    style LLM fill:#ffdac1,color:#3d3556
</div>

**适用场景**：财务报表查询、数据分析、ERP/CRM 系统对接
**代表工具**：LangChain SQL Agent、Vanna.ai

### 2.3 向量知识库（最常用⭐）

这是目前 **最主流** 的 Agent 知识库形态。将文档切片后转化为向量，通过语义相似度检索。

<div class="mermaid">
graph TB
    subgraph 构建阶段["📥 知识入库阶段"]
        Docs["📄 原始文档<br/>PDF / Word / Markdown"] --> Parser["✂️ 文档解析<br/>+ 切片"]
        Parser --> Embed["🔢 Embedding 模型<br/>文本→向量"]
        Embed --> VDB[("🗄️ 向量数据库<br/>存储向量 + 元数据")]
    end

    subgraph 检索阶段["🔍 检索阶段"]
        Query["❓ 用户问题"] --> QEmbed["🔢 问题向量化"]
        QEmbed --> Search["📐 相似度计算<br/>余弦 / 点积"]
        Search --> VDB
        VDB --> TopK["🏆 Top-K 相关片段"]
        TopK --> LLM["🧠 LLM + 上下文<br/>生成答案"]
    end

    style Docs fill:#ffd6e0,color:#3d3556
    style Parser fill:#ffdac1,color:#3d3556
    style Embed fill:#c7ceea,color:#3d3556
    style VDB fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style Query fill:#ffd6e0,color:#3d3556
    style QEmbed fill:#c7ceea,color:#3d3556
    style Search fill:#e2f0cb,color:#3d3556
    style TopK fill:#bee3db,color:#3d3556
    style LLM fill:#ffdac1,color:#3d3556
</div>

**适用场景**：私有文档问答、企业内部知识管理、代码库搜索
**代表工具**：Chroma、FAISS、Milvus、Weaviate、Qdrant

### 2.4 图谱知识库

将知识以"实体-关系-实体"三元组形式存储，支持复杂的关联推理。

<div class="mermaid">
graph LR
    A["🏢 阿里巴巴"] -->|"旗下产品"| B["🛒 淘宝"]
    A -->|"旗下产品"| C["☁️ 阿里云"]
    A -->|"旗下产品"| D["🤖 通义千问 Qwen"]
    D -->|"基于技术"| E["🧠 Transformer"]
    D -->|"竞品"| F["💬 ChatGPT"]
    F -->|"开发者"| G["🏢 OpenAI"]

    style A fill:#c7ceea,color:#3d3556
    style B fill:#b5ead7,color:#3d3556
    style C fill:#b5ead7,color:#3d3556
    style D fill:#ffd6e0,color:#3d3556,stroke:#f0a0b0,stroke-width:2px
    style E fill:#ffdac1,color:#3d3556
    style F fill:#e2f0cb,color:#3d3556
    style G fill:#bee3db,color:#3d3556
</div>

**适用场景**：复杂关系推理、医疗知识图谱、反欺诈、推荐系统
**代表工具**：Neo4j、NebulaGraph、Microsoft GraphRAG

### 2.5 混合知识库（进阶⭐⭐）

结合向量检索的语义能力与关键词检索的精确能力，以及可选的图谱推理。

<div class="mermaid">
graph TB
    Q["❓ 用户查询"] --> Hybrid["🔀 混合检索器"]
    Hybrid --> Vec["📐 向量检索<br/>语义相似度"]
    Hybrid --> BM25["🔑 BM25关键词检索<br/>精确匹配"]
    Hybrid --> Graph["🕸️ 图谱推理<br/>关联关系"]
    Vec --> Rerank["⚖️ 重排序<br/>Re-Ranker"]
    BM25 --> Rerank
    Graph --> Rerank
    Rerank --> Context["📋 最优上下文"]
    Context --> LLM["🧠 LLM"]

    style Q fill:#ffd6e0,color:#3d3556
    style Hybrid fill:#c7ceea,color:#3d3556
    style Vec fill:#b5ead7,color:#3d3556
    style BM25 fill:#ffdac1,color:#3d3556
    style Graph fill:#e2f0cb,color:#3d3556
    style Rerank fill:#bee3db,color:#3d3556
    style Context fill:#bee3db,color:#3d3556
    style LLM fill:#ffdac1,color:#3d3556
</div>

---

## 三、核心技术深度解析

### 3.1 文档解析与切片

知识库质量的第一关。原始文档需要先解析、清洗，再切割成合适大小的片段（Chunk）。

<div class="mermaid">
graph LR
    subgraph 输入["📥 文档来源"]
        PDF["📄 PDF"]
        Word["📝 Word"]
        MD["📋 Markdown"]
        Web["🌐 网页"]
        Code["💻 代码"]
    end

    subgraph 解析["🔧 解析处理"]
        OCR["OCR 识别<br/>扫描件处理"]
        Extract["内容提取<br/>去除噪声"]
        Clean["清洗规范化<br/>标点/编码"]
    end

    subgraph 切片["✂️ 切片策略"]
        Fixed["固定大小切片<br/>512 tokens"]
        Semantic["语义切片<br/>按段落/章节"]
        Recursive["递归切片<br/>LangChain默认"]
        Overlap["滑动窗口<br/>保留上下文overlap"]
    end

    PDF & Word & MD & Web & Code --> OCR --> Extract --> Clean
    Clean --> Fixed & Semantic & Recursive & Overlap

    style PDF fill:#ffd6e0,color:#3d3556
    style Word fill:#ffd6e0,color:#3d3556
    style MD fill:#ffd6e0,color:#3d3556
    style Web fill:#ffd6e0,color:#3d3556
    style Code fill:#ffd6e0,color:#3d3556
    style OCR fill:#c7ceea,color:#3d3556
    style Extract fill:#c7ceea,color:#3d3556
    style Clean fill:#c7ceea,color:#3d3556
    style Fixed fill:#b5ead7,color:#3d3556
    style Semantic fill:#b5ead7,color:#3d3556
    style Recursive fill:#b5ead7,color:#3d3556
    style Overlap fill:#b5ead7,color:#3d3556
</div>

**切片策略对比**：

| 策略 | 优点 | 缺点 | 适用场景 |
|------|------|------|---------|
| **固定大小** | 简单快速 | 可能截断语义 | 初步验证 |
| **语义切片** | 保留完整语义 | 实现复杂 | 正式生产 |
| **递归切片** | 灵活平衡 | 参数调优难 | 通用场景 |
| **滑动窗口** | 上下文连续 | 存储冗余 | 长文档 |

### 3.2 Embedding（嵌入）

将文本转化为高维向量，使语义相近的文本在向量空间中距离相近。

<div class="mermaid">
graph LR
    T1["'苹果手机'"] --> E["🔢 Embedding 模型<br/>text-embedding-3 /<br/>bge-m3 / m3e"]
    T2["'iPhone'"] --> E
    T3["'香蕉'"] --> E
    E --> V1["[0.82, -0.31, 0.56, ...]"]
    E --> V2["[0.79, -0.28, 0.61, ...]"]
    E --> V3["[0.12, 0.74, -0.33, ...]"]
    V1 <-->|"cos=0.97<br/>非常相近"| V2
    V1 <-->|"cos=0.12<br/>差异很大"| V3

    style T1 fill:#ffd6e0,color:#3d3556
    style T2 fill:#ffd6e0,color:#3d3556
    style T3 fill:#e2f0cb,color:#3d3556
    style E fill:#c7ceea,color:#3d3556
    style V1 fill:#b5ead7,color:#3d3556
    style V2 fill:#b5ead7,color:#3d3556
    style V3 fill:#bee3db,color:#3d3556
</div>

**主流 Embedding 模型对比**：

| 模型 | 提供方 | 维度 | 语言 | 是否本地 | 特点 |
|------|-------|------|------|---------|------|
| `text-embedding-3-large` | OpenAI | 3072 | 多语言 | ❌ 云端 | 效果最好 |
| `bge-m3` | 智源研究院 | 1024 | 多语言 | ✅ 本地 | 中英文优秀 |
| `bge-large-zh` | 智源研究院 | 1024 | 中文 | ✅ 本地 | 中文最佳 |
| `m3e-large` | 网易有道 | 768 | 中文 | ✅ 本地 | 轻量好用 |
| `nomic-embed-text` | Nomic | 768 | 英文 | ✅ 本地 | 开源高性能 |

> **本地方案推荐**：搭配 Qwen 使用时，推荐 `bge-m3` 或 `bge-large-zh`，完全离线，中文效果优秀。

### 3.3 向量数据库选型

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
quadrantChart
    title 向量数据库选型矩阵
    x-axis "轻量 / 嵌入式" --> "重量 / 分布式"
    y-axis "功能简单" --> "功能丰富"
    quadrant-1 "企业生产首选"
    quadrant-2 "功能强大但复杂"
    quadrant-3 "快速原型"
    quadrant-4 "轻量但功能足"
    "FAISS": [0.1, 0.2]
    "Chroma": [0.25, 0.45]
    "Qdrant": [0.45, 0.75]
    "Weaviate": [0.6, 0.8]
    "Milvus": [0.85, 0.9]
    "pgvector": [0.35, 0.55]
</div>

| 数据库 | 部署方式 | 数据量 | 特点 | 推荐场景 |
|--------|---------|--------|------|---------|
| **FAISS** | 嵌入式 | 中小 | 纯内存，速度极快 | 研究/原型 |
| **Chroma** | 嵌入式/服务 | 中小 | 开箱即用，API友好 | 个人/小团队 ⭐ |
| **Qdrant** | 独立服务 | 大 | Rust编写，性能强 | 生产环境 ⭐ |
| **Milvus** | 分布式 | 超大 | 云原生，企业级 | 大规模企业 |
| **pgvector** | PostgreSQL插件 | 中 | 复用已有PG | 已有PG用户 |
| **Weaviate** | 独立服务 | 大 | 内置GraphQL | 复杂查询 |

### 3.4 RAG 检索策略进阶

基础 RAG 已经很强大，但有多种进阶策略可以大幅提升效果：

<div class="mermaid">
graph TB
    subgraph Basic["🟢 基础 RAG"]
        B1["查询 → 向量化 → Top-K → 生成"]
    end

    subgraph Advanced["🔵 进阶 RAG"]
        A1["HyDE<br/>假设文档嵌入"]
        A2["多查询检索<br/>Multi-Query"]
        A3["RAG Fusion<br/>结果融合"]
        A4["Self-RAG<br/>自我反思"]
    end

    subgraph Modular["🟣 模块化 RAG"]
        M1["查询改写<br/>Query Rewrite"]
        M2["重排序<br/>Reranker"]
        M3["知识图谱融合<br/>GraphRAG"]
        M4["长上下文压缩<br/>LongLLMLingua"]
    end

    Basic -->|升级| Advanced
    Advanced -->|升级| Modular

    style Basic fill:#b5ead7,color:#3d3556
    style Advanced fill:#c7ceea,color:#3d3556
    style Modular fill:#ffdac1,color:#3d3556
    style A1 fill:#e2f0cb,color:#3d3556
    style A2 fill:#e2f0cb,color:#3d3556
    style A3 fill:#e2f0cb,color:#3d3556
    style A4 fill:#e2f0cb,color:#3d3556
    style M1 fill:#ffd6e0,color:#3d3556
    style M2 fill:#ffd6e0,color:#3d3556
    style M3 fill:#ffd6e0,color:#3d3556
    style M4 fill:#ffd6e0,color:#3d3556
</div>

---

## 四、主流搭建方式对比

### 4.1 全景对比

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph LR
    ROOT(["🛠️ 知识库搭建方案"])

    ROOT --> L1["🟢 低代码平台<br/>（拖拽配置）"]
    ROOT --> L2["🔵 开发框架<br/>（代码开发）"]
    ROOT --> L3["🟣 一体化工具<br/>（本地优先）"]

    L1 --> D1["Dify<br/>功能最全面"]
    L1 --> D2["FastGPT<br/>专注知识库"]
    L1 --> D3["RAGFlow<br/>文档解析强"]

    L2 --> F1["LangChain<br/>最广泛"]
    L2 --> F2["LlamaIndex<br/>RAG专精"]
    L2 --> F3["Haystack<br/>企业级"]

    L3 --> A1["AnythingLLM<br/>桌面端"]
    L3 --> A2["Jan.ai<br/>极简本地"]
    L3 --> A3["Open WebUI<br/>Ollama前端"]

    style ROOT fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style L1 fill:#b5ead7,color:#3d3556
    style L2 fill:#ffdac1,color:#3d3556
    style L3 fill:#ffd6e0,color:#3d3556
    style D1 fill:#bee3db,color:#3d3556
    style D2 fill:#bee3db,color:#3d3556
    style D3 fill:#bee3db,color:#3d3556
    style F1 fill:#e2f0cb,color:#3d3556
    style F2 fill:#e2f0cb,color:#3d3556
    style F3 fill:#e2f0cb,color:#3d3556
    style A1 fill:#ffd6e0,color:#3d3556
    style A2 fill:#ffd6e0,color:#3d3556
    style A3 fill:#ffd6e0,color:#3d3556
</div>

### 4.2 方案一：Dify（低代码，强烈推荐新手⭐）

Dify 是目前最流行的开源 AI 应用开发平台，内置知识库管理，支持本地模型。

<div class="mermaid">
graph TB
    subgraph Dify架构["🌟 Dify 平台架构"]
        Web["🖥️ Web UI<br/>可视化操作"] --> API["🔌 API Server"]
        API --> KB2["📚 知识库管理<br/>上传/解析/索引"]
        API --> App["🤖 应用编排<br/>聊天/工作流/Agent"]
        KB2 --> VecDB2["🗄️ 向量存储<br/>Weaviate / pgvector"]
        App --> LLM2["🧠 模型接入<br/>Ollama / OpenAI / Qwen"]
    end

    style Web fill:#ffd6e0,color:#3d3556
    style API fill:#c7ceea,color:#3d3556
    style KB2 fill:#b5ead7,color:#3d3556
    style App fill:#ffdac1,color:#3d3556
    style VecDB2 fill:#bee3db,color:#3d3556
    style LLM2 fill:#e2f0cb,color:#3d3556
</div>

```bash
# 一键部署 Dify（Docker Compose）
git clone https://github.com/langgenius/dify.git
cd dify/docker
cp .env.example .env
docker compose up -d

# 访问 http://localhost/  完成初始化
```

**优点**：图形化界面，无需写代码，功能完整（知识库/工作流/API/监控）
**缺点**：重量级，需要 Docker 环境，定制化能力有限

### 4.3 方案二：RAGFlow（文档解析最强⭐）

专注于高质量文档解析的 RAG 引擎，对 PDF、表格、图片等复杂文档处理出色。

```bash
# 部署 RAGFlow
git clone https://github.com/infiniflow/ragflow.git
cd ragflow/docker
docker compose up -d

# 访问 http://localhost:80
```

**核心优势**：业界最强的 PDF/表格/PPT 解析能力，支持版面分析和 OCR

### 4.4 方案三：LlamaIndex（开发者首选⭐⭐）

Python 框架，专为 RAG 场景设计，灵活可定制，支持几乎所有向量数据库和 LLM。

```python
# 最简知识库示例
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.huggingface import HuggingFaceEmbedding

# 配置本地 Qwen 模型
llm = Ollama(model="qwen2.5:7b", base_url="http://localhost:11434")
embed_model = HuggingFaceEmbedding(model_name="BAAI/bge-large-zh-v1.5")

# 读取本地文档并建立索引
documents = SimpleDirectoryReader("./my_docs").load_data()
index = VectorStoreIndex.from_documents(documents, embed_model=embed_model)

# 查询
query_engine = index.as_query_engine(llm=llm)
response = query_engine.query("文档中说了什么？")
print(response)
```

### 4.5 方案四：AnythingLLM（桌面端本地知识库⭐）

桌面应用，安装即用，对个人用户极友好，支持 Ollama 本地模型和本地知识库。

**特点**：无需命令行，图形化界面，一键导入文档，直接对接 Ollama

### 4.6 方案横向对比

| 维度 | Dify | RAGFlow | LlamaIndex | AnythingLLM | LangChain |
|------|------|---------|------------|-------------|-----------|
| **上手难度** | ⭐ 简单 | ⭐ 简单 | ⭐⭐⭐ 需编码 | ⭐ 最简单 | ⭐⭐⭐ 需编码 |
| **文档解析** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **本地模型** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **可定制性** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| **生产就绪** | ✅ | ✅ | 需自行封装 | ❌ | 需自行封装 |
| **中文支持** | ✅ 优秀 | ✅ 优秀 | ✅ 良好 | ✅ 良好 | ✅ 良好 |
| **VSCode 集成** | ❌ | ❌ | ✅ | ❌ | ✅ |
| **适合你的场景** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |

---

## 五、实战：基于本地 Qwen + VSCode + Copilot 搭建知识库

### 5.1 你的环境分析

<div class="mermaid">
graph TB
    subgraph 你的现有环境["💻 你的现有环境"]
        Qwen["🤖 Qwen 大模型<br/>本地部署"]
        VSCode["🖊️ VSCode<br/>代码编辑器"]
        Copilot["🤖 GitHub Copilot<br/>代码补全"]
    end

    subgraph 可以增强["🚀 可以添加的知识库能力"]
        Ollama["🦙 Ollama<br/>模型管理服务"]
        Continue["🔌 Continue 插件<br/>VSCode AI助手"]
        KB3["📚 本地知识库<br/>Chroma + bge-m3"]
        Dify2["🌟 Dify<br/>知识库管理平台"]
    end

    Qwen -->|"接入"| Ollama
    VSCode -->|"安装"| Continue
    Copilot -->|"并存"| Continue
    Ollama --> Continue
    KB3 --> Continue
    Ollama --> Dify2
    KB3 --> Dify2

    style Qwen fill:#ffd6e0,color:#3d3556
    style VSCode fill:#c7ceea,color:#3d3556
    style Copilot fill:#b5ead7,color:#3d3556
    style Ollama fill:#ffdac1,color:#3d3556,stroke:#ffb380,stroke-width:2px
    style Continue fill:#ffdac1,color:#3d3556,stroke:#ffb380,stroke-width:2px
    style KB3 fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style Dify2 fill:#e2f0cb,color:#3d3556
</div>

### 5.2 推荐架构：两套方案

<div class="mermaid">
graph LR
    subgraph 方案A["🔵 方案A：VSCode 深度集成（开发者）"]
        A_VS["VSCode"] --> A_Cont["Continue 插件"]
        A_Cont --> A_Oll["Ollama<br/>(Qwen2.5)"]
        A_Cont --> A_KB["本地知识库<br/>(Chroma DB)"]
        A_KB --> A_Emb["bge-m3<br/>Embedding"]
    end

    subgraph 方案B["🟢 方案B：Dify 平台（全功能）"]
        B_VS["VSCode"] --> B_Code["日常编码"]
        B_Dify["Dify 平台"] --> B_Oll2["Ollama<br/>(Qwen2.5)"]
        B_Dify --> B_KB2["知识库<br/>管理"]
        B_Dify --> B_App["聊天/API<br/>应用"]
    end

    style 方案A fill:#c7ceea,color:#3d3556
    style 方案B fill:#b5ead7,color:#3d3556
    style A_VS fill:#ffd6e0,color:#3d3556
    style A_Cont fill:#ffdac1,color:#3d3556
    style A_Oll fill:#e2f0cb,color:#3d3556
    style A_KB fill:#b5ead7,color:#3d3556
    style A_Emb fill:#bee3db,color:#3d3556
    style B_VS fill:#ffd6e0,color:#3d3556
    style B_Code fill:#ffd6e0,color:#3d3556
    style B_Dify fill:#ffdac1,color:#3d3556
    style B_Oll2 fill:#e2f0cb,color:#3d3556
    style B_KB2 fill:#b5ead7,color:#3d3556
    style B_App fill:#bee3db,color:#3d3556
</div>

### 5.3 方案 A：VSCode + Continue + 本地知识库（完整教程）

#### 第一步：部署 Qwen 到 Ollama

```bash
# 1. 安装 Ollama（macOS）
curl -fsSL https://ollama.com/install.sh | sh

# Windows 用户：访问 https://ollama.com 下载安装包

# 2. 拉取 Qwen2.5 模型（选择适合你硬件的规格）
ollama pull qwen2.5:7b      # 7B，显存需求 ~8GB
ollama pull qwen2.5:14b     # 14B，显存需求 ~16GB

# 3. 拉取 Embedding 模型（中文知识库必备）
ollama pull bge-m3          # 多语言 Embedding，推荐
ollama pull nomic-embed-text  # 英文场景备选

# 4. 验证部署
ollama list                 # 查看已安装模型
curl http://localhost:11434/api/generate \
  -d '{"model":"qwen2.5:7b","prompt":"你好"}'
```

#### 第二步：在 VSCode 安装并配置 Continue

```bash
# 1. 在 VSCode 扩展市场搜索 "Continue" 并安装
# 或者通过命令行安装
code --install-extension Continue.continue
```

配置文件 `~/.continue/config.json`（Continue 配置文件路径）：

```json
{
  "models": [
    {
      "title": "Qwen2.5-7B (本地)",
      "provider": "ollama",
      "model": "qwen2.5:7b",
      "apiBase": "http://localhost:11434"
    }
  ],
  "embeddingsProvider": {
    "provider": "ollama",
    "model": "bge-m3",
    "apiBase": "http://localhost:11434"
  },
  "contextProviders": [
    {
      "name": "code",
      "params": {}
    },
    {
      "name": "docs",
      "params": {}
    },
    {
      "name": "folder",
      "params": {}
    }
  ],
  "slashCommands": [
    {
      "name": "edit",
      "description": "修改代码"
    },
    {
      "name": "comment",
      "description": "添加注释"
    },
    {
      "name": "share",
      "description": "分享对话"
    }
  ]
}
```

#### 第三步：建立本地知识库

```bash
# 创建知识库项目
mkdir ~/my-knowledge-base && cd ~/my-knowledge-base
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

# 安装依赖
pip install llama-index \
            llama-index-llms-ollama \
            llama-index-embeddings-ollama \
            chromadb
```

```python
# build_kb.py —— 构建本地知识库
from llama_index.core import (
    VectorStoreIndex,
    SimpleDirectoryReader,
    StorageContext,
    Settings,
)
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding
from llama_index.vector_stores.chroma import ChromaVectorStore
import chromadb

# ① 配置本地 Qwen 和 Embedding
Settings.llm = Ollama(model="qwen2.5:7b", base_url="http://localhost:11434")
Settings.embed_model = OllamaEmbedding(
    model_name="bge-m3",
    base_url="http://localhost:11434"
)

# ② 连接本地 Chroma 向量库（数据持久化到磁盘）
chroma_client = chromadb.PersistentClient(path="./chroma_db")
chroma_collection = chroma_client.get_or_create_collection("my_kb")
vector_store = ChromaVectorStore(chroma_collection=chroma_collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

# ③ 读取本地文档（把你的文档放到 ./docs 目录）
documents = SimpleDirectoryReader(
    "./docs",
    recursive=True,
    required_exts=[".pdf", ".md", ".txt", ".docx"]
).load_data()

# ④ 建立索引（首次运行会调用 Embedding 模型）
index = VectorStoreIndex.from_documents(
    documents,
    storage_context=storage_context,
    show_progress=True
)

print(f"✅ 知识库构建完成，共处理 {len(documents)} 个文档")
```

```python
# query_kb.py —— 查询知识库
from llama_index.core import VectorStoreIndex, StorageContext, Settings
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding
from llama_index.vector_stores.chroma import ChromaVectorStore
import chromadb

Settings.llm = Ollama(model="qwen2.5:7b", base_url="http://localhost:11434")
Settings.embed_model = OllamaEmbedding(
    model_name="bge-m3",
    base_url="http://localhost:11434"
)

# 加载已有的向量库
chroma_client = chromadb.PersistentClient(path="./chroma_db")
chroma_collection = chroma_client.get_collection("my_kb")
vector_store = ChromaVectorStore(chroma_collection=chroma_collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

index = VectorStoreIndex.from_vector_store(vector_store)
query_engine = index.as_query_engine(similarity_top_k=3)

# 开始问答
while True:
    q = input("\n❓ 请输入问题（输入 q 退出）：")
    if q.lower() == "q":
        break
    response = query_engine.query(q)
    print(f"\n💬 回答：{response}")
    print(f"\n📎 来源：{[n.metadata.get('file_name') for n in response.source_nodes]}")
```

#### 第四步：在 VSCode 中使用知识库

<div class="mermaid">
sequenceDiagram
    participant You as 👨‍💻 你
    participant VS as VSCode
    participant Cont as Continue 插件
    participant Qwen as 🤖 Qwen (Ollama)
    participant KB4 as 📚 本地知识库

    You->>VS: 在聊天框输入 @docs 或 @folder
    VS->>Cont: 触发上下文检索
    Cont->>KB4: 向量检索相关片段
    KB4-->>Cont: 返回 Top-K 片段
    Cont->>Qwen: Prompt = 片段 + 问题
    Qwen-->>Cont: 生成回答
    Cont-->>VS: 显示回答 + 引用来源
    VS-->>You: 展示结果
</div>

在 VSCode 的 Continue 聊天窗口中：
- 输入 `@docs` — 引用已索引的文档知识库
- 输入 `@folder` — 引用当前工作区文件夹
- 输入 `@file` — 引用特定文件
- 输入 `@code` — 引用代码片段

### 5.4 方案 B：Dify + Ollama（推荐给日常文档问答）

```bash
# ① 部署 Dify
git clone https://github.com/langgenius/dify.git
cd dify/docker
cp .env.example .env

# ② 修改 .env 配置 Ollama
# 找到以下行并修改：
# OLLAMA_BASE_URL=http://host.docker.internal:11434

docker compose up -d

# ③ 访问 http://localhost 完成初始化设置
```

**Dify 知识库搭建流程**：

<div class="mermaid">
graph LR
    S1["1️⃣ 登录 Dify<br/>创建账号"] --> S2["2️⃣ 模型设置<br/>添加 Ollama 模型<br/>选择 qwen2.5:7b<br/>+ bge-m3 嵌入"]
    S2 --> S3["3️⃣ 创建知识库<br/>点击「知识库」<br/>→「创建知识库」"]
    S3 --> S4["4️⃣ 上传文档<br/>拖拽 PDF/Word/MD<br/>选择分割策略"]
    S4 --> S5["5️⃣ 等待索引<br/>自动解析 + 向量化<br/>（Qwen本地处理）"]
    S5 --> S6["6️⃣ 创建应用<br/>选择「聊天助手」<br/>绑定知识库"]
    S6 --> S7["7️⃣ 发布使用<br/>网页/API/嵌入<br/>多种调用方式"]

    style S1 fill:#ffd6e0,color:#3d3556
    style S2 fill:#c7ceea,color:#3d3556
    style S3 fill:#b5ead7,color:#3d3556
    style S4 fill:#ffdac1,color:#3d3556
    style S5 fill:#e2f0cb,color:#3d3556
    style S6 fill:#bee3db,color:#3d3556
    style S7 fill:#ffd6e0,color:#3d3556
</div>

### 5.5 GitHub Copilot 与本地知识库共存

Copilot 和本地知识库（Continue）是 **互补关系，不冲突**：

| 场景 | 使用 GitHub Copilot | 使用 Continue + 本地知识库 |
|------|--------------------|-----------------------|
| **代码补全** | ✅ 首选（云端强模型） | 可用（本地速度快） |
| **代码生成** | ✅ 适合通用代码 | ✅ 适合项目特定代码 |
| **查询私有文档** | ❌ 不支持 | ✅ 核心能力 |
| **公司内部 API** | ❌ 不知道 | ✅ 索引后可知 |
| **代码库上下文** | 有限（仅当前文件） | ✅ 可索引整个仓库 |
| **离线使用** | ❌ 需联网 | ✅ 完全离线 |
| **数据隐私** | ⚠️ 上传云端 | ✅ 数据不出本地 |

> **最佳实践**：日常编码用 Copilot（云端最强）+ 查询私有知识/公司文档用 Continue + Qwen（本地安全）。两者可在 VSCode 中同时运行，互不干扰。

### 5.6 完整架构总览

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '14px'}}}%%
graph TB
    subgraph VSCode环境["🖊️ VSCode 开发环境"]
        CP["☁️ GitHub Copilot<br/>云端代码补全"]
        Cont2["🔌 Continue 插件<br/>本地AI助手"]
    end

    subgraph 本地服务["🖥️ 本地服务层"]
        Olla["🦙 Ollama 服务<br/>localhost:11434"]
        QwenM["🤖 Qwen2.5-7B<br/>对话/生成"]
        BgeM["🔢 bge-m3<br/>Embedding"]
        ChromaM["🗄️ Chroma DB<br/>向量存储"]
    end

    subgraph 知识来源["📂 知识来源"]
        MyDocs["📄 你的文档<br/>PDF/Word/MD"]
        CodeBase["💻 代码库<br/>项目仓库"]
        Notes["📝 笔记/Wiki<br/>个人知识"]
    end

    Cont2 <-->|"OpenAI兼容API"| Olla
    Olla --- QwenM
    Olla --- BgeM
    Cont2 <-->|"向量检索"| ChromaM
    BgeM -->|"向量化"| ChromaM
    MyDocs & CodeBase & Notes -->|"索引建立"| ChromaM

    CP <-->|"HTTPS"| Internet["☁️ GitHub 云端"]

    style CP fill:#c7ceea,color:#3d3556
    style Cont2 fill:#ffdac1,color:#3d3556,stroke:#ffb380,stroke-width:2px
    style Olla fill:#b5ead7,color:#3d3556
    style QwenM fill:#ffd6e0,color:#3d3556
    style BgeM fill:#ffd6e0,color:#3d3556
    style ChromaM fill:#b5ead7,color:#3d3556,stroke:#81c7b5,stroke-width:2px
    style MyDocs fill:#e2f0cb,color:#3d3556
    style CodeBase fill:#e2f0cb,color:#3d3556
    style Notes fill:#e2f0cb,color:#3d3556
    style Internet fill:#bee3db,color:#3d3556
</div>

---

## 六、知识库优化与进阶技巧

### 6.1 常见问题与解决

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| **回答与文档不符** | 切片粒度不当，语义被截断 | 调小 chunk_size，增大 overlap |
| **检索不到相关内容** | Embedding 模型效果差 | 换用 bge-m3 或 bge-large-zh |
| **回答内容过于模糊** | Top-K 太小，上下文不足 | 增大 similarity_top_k（3→5） |
| **速度太慢** | 模型太大或 CPU 推理 | 换 7B 模型，确认 GPU 加速 |
| **中文效果差** | 使用了英文 Embedding 模型 | 必须使用 bge-m3 或 bge-large-zh |

### 6.2 知识库质量评估

<div class="mermaid">
graph LR
    subgraph 评估维度["📊 评估维度"]
        R["🎯 召回率<br/>Recall<br/>找到了吗？"]
        P["🎯 准确率<br/>Precision<br/>找对了吗？"]
        F["💬 忠实度<br/>Faithfulness<br/>没有幻觉吗？"]
        A["🧩 答案相关性<br/>Relevancy<br/>回答了问题吗？"]
    end

    subgraph 工具["🛠️ 评估工具"]
        Ragas["RAGAS<br/>自动化RAG评估"]
        Trulens["TruLens<br/>链路追踪"]
        Manual["人工抽检<br/>定期验证"]
    end

    R & P & F & A --> Ragas & Trulens & Manual

    style R fill:#ffd6e0,color:#3d3556
    style P fill:#c7ceea,color:#3d3556
    style F fill:#b5ead7,color:#3d3556
    style A fill:#ffdac1,color:#3d3556
    style Ragas fill:#e2f0cb,color:#3d3556
    style Trulens fill:#e2f0cb,color:#3d3556
    style Manual fill:#bee3db,color:#3d3556
</div>

### 6.3 知识库演进路径

<div class="mermaid">
graph LR
    L1["🌱 入门阶段<br/>单文档 + Chroma<br/>+ Ollama 直连"] -->|熟悉后| L2["🌿 进阶阶段<br/>多文档分类管理<br/>+ Dify 平台<br/>+ 混合检索"]
    L2 -->|需求增长| L3["🌳 成熟阶段<br/>自动化入库流水线<br/>+ Reranker<br/>+ GraphRAG"]
    L3 -->|团队规模| L4["🏢 企业阶段<br/>Milvus 分布式<br/>+ 权限管理<br/>+ 可观测性"]

    style L1 fill:#b5ead7,color:#3d3556
    style L2 fill:#ffdac1,color:#3d3556
    style L3 fill:#c7ceea,color:#3d3556
    style L4 fill:#ffd6e0,color:#3d3556
</div>

---

## 七、总结

### 7.1 核心要点回顾

<div class="mermaid">
%%{init: {'theme': 'base', 'themeVariables': {'fontSize': '15px'}}}%%
graph TB
    Core(["📚 Agent 知识库<br/>核心要点"])

    Core --> T1["🔑 本质<br/>让Agent能检索<br/>私有/实时知识"]
    Core --> T2["🗂️ 四大类型<br/>结构化/向量<br/>图谱/混合"]
    Core --> T3["⚙️ 核心技术<br/>切片→Embedding<br/>→向量库→RAG"]
    Core --> T4["🛠️ 主流方案<br/>Dify/RAGFlow<br/>LlamaIndex/Continue"]
    Core --> T5["🚀 你的方案<br/>Ollama+Qwen<br/>+Continue+Chroma"]

    style Core fill:#c7ceea,color:#3d3556,stroke:#a090d8,stroke-width:2px
    style T1 fill:#ffd6e0,color:#3d3556
    style T2 fill:#b5ead7,color:#3d3556
    style T3 fill:#ffdac1,color:#3d3556
    style T4 fill:#e2f0cb,color:#3d3556
    style T5 fill:#bee3db,color:#3d3556,stroke:#81c7b5,stroke-width:2px
</div>

### 7.2 针对你的情况的行动建议

1. **第一步（今天）**：在本地安装 Ollama，`ollama pull qwen2.5:7b` 和 `ollama pull bge-m3`
2. **第二步（这周）**：在 VSCode 安装 Continue 插件，按照第五章配置接入 Ollama
3. **第三步（下周）**：把你最常查阅的文档（技术手册、API文档等）整理到一个文件夹，运行 `build_kb.py` 建立向量索引
4. **第四步（一个月后）**：如果对 Dify 的图形化管理感兴趣，再部署 Dify 平台，把知识库迁移进去统一管理

> **你的优势**：已有本地 Qwen 大模型，数据不离本地，隐私安全有保障。Continue 插件与 GitHub Copilot 完全共存，各司其职，相得益彰。

---

*本文持续更新，欢迎在 Issues 中提问或补充。*
