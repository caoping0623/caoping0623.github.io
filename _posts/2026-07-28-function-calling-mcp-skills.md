---
title: "把 Function Calling、MCP、Skills 一次讲清楚"
date: 2026-07-28
categories: [人工智能]
tags: [Function Calling, MCP, Skills, AI Agent, Cursor, Hermes, 工具调用]
excerpt: "这三个词经常被混着说，但它们其实处在完全不同的层次上：Function Calling 是模型的能力，MCP 是工具的接口协议，Skills 是给 Agent 的操作手册。本文从零讲起，每个概念配一个能跑的例子，讲清区别、讲清怎么搭配，并给出在 Cursor 和 Hermes Agent 里的真实配置方法。"
---

> 最近半年，只要聊到 AI Agent，"function calling""MCP""skills"这三个词几乎必然同时出现。很多人的困惑不是"它们是什么"，而是**"它们到底是不是一回事？我该用哪个？"**
>
> 这篇文章写给还没上手的人。看完你应该能回答：这三样东西各自解决什么问题、边界在哪、能不能一起用、在 Cursor 和 Hermes Agent 里分别怎么配。

---

## 一、先建立直觉：它们根本不在一个层次上

### 1.1 一句话定义

| 概念 | 一句话说明 | 它是什么"东西" |
| --- | --- | --- |
| **Function Calling** | 让大模型能输出一段"我要调用哪个函数、传什么参数"的结构化请求 | 一种**模型能力** + 一套 API 约定 |
| **MCP**（Model Context Protocol） | 一个开放协议，规定外部工具/数据源如何把自己的能力"自我描述"并暴露给 AI | 一套**通信协议** |
| **Skills** | 一个 Markdown 文件（`SKILL.md`），教 Agent"这类任务应该按什么步骤、什么规范去做" | 一份**操作手册（提示词）** |

### 1.2 用"招人"来理解

把 Agent 想象成你新招的一位助理：

- **Function Calling** = 这位助理**会不会填表单**。你给他一沓空白表单（工具定义），他能准确判断该填哪张、每个格子填什么。这是他的**基本能力**，不会填就啥也干不了。
- **MCP** = 公司的**标准接口规范**。以前每个部门（GitHub、数据库、Figma）的表单格式都不一样，助理得为每个部门单独学一遍；有了 MCP，所有部门统一用一种表单格式，助理学一次就能对接所有部门。
- **Skills** = 你写给他的**岗位 SOP 手册**。"发布周报时，先从 Jira 拉数据，再按这个模板排版，标题必须是这个格式……"手册不给他任何新能力，但决定了他做事的**质量和一致性**。

三者的关系是**叠加**的，不是替代：

<div class="mermaid">
graph TB
    U["用户：帮我把本周的 Jira 工单整理成周报"]

    subgraph L3["📖 Skills 层 —— 怎么做"]
        S["weekly-report/SKILL.md<br/>步骤 / 模板 / 规范"]
    end

    subgraph L2["🧠 Function Calling 层 —— 决定做什么"]
        B["LLM 读取工具清单<br/>输出 JSON 调用请求"]
    end

    subgraph L1["🔌 MCP 层 —— 能做什么"]
        M1[Jira MCP Server]
        M2[文件系统 MCP Server]
        M3[内置工具 terminal / read_file]
    end

    U --> S
    S --> B
    B --> M1
    B --> M2
    B --> M3
    M1 --> R[结果回填给 LLM]
    M2 --> R
    M3 --> R
    R --> B
    B --> OUT[生成周报]

    style U fill:#b5ead7,color:#3d3556
    style S fill:#ffdac1,color:#3d3556
    style B fill:#c7ceea,color:#3d3556
    style M1 fill:#bee3db,color:#3d3556
    style M2 fill:#bee3db,color:#3d3556
    style M3 fill:#bee3db,color:#3d3556
    style R fill:#e2f0cb,color:#3d3556
    style OUT fill:#b5ead7,color:#3d3556
</div>

记住这句话，后面所有内容都是它的展开：

> **Function Calling 决定"模型能不能调工具"，MCP 决定"工具怎么接进来"，Skills 决定"这些工具该怎么用"。**

---

## 二、Function Calling：让模型学会"填表单"

### 2.1 它解决了什么问题

大模型本身有三个硬伤：**不知道实时信息**、**不会算数**、**改变不了外部世界**。

你问 GPT"苏州现在几度"，它只能编。因为它的知识停在训练截止日，而且它唯一的输出方式是生成文本。

Function Calling 的思路很朴素：**既然模型只会输出文本，那就让它输出一段"格式规整的文本"，程序解析这段文本去真正执行操作，再把结果喂回去。**

关键点在于：**模型自己不执行任何函数**。它只负责"决定调哪个、参数是什么"，执行是你的代码干的。这个误解非常常见，必须掰正。

### 2.2 完整流程

<div class="mermaid">
sequenceDiagram
    participant U as 用户
    participant A as 你的应用代码
    participant L as LLM
    participant F as 真实函数 get_weather

    U->>A: 苏州今天天气怎么样？
    A->>L: 消息 + tools 工具定义清单
    L-->>A: 我要调用 get_weather，参数 city=Suzhou
    Note over L,A: 模型只输出 JSON，不执行
    A->>F: 真正执行函数
    F-->>A: 温度 32 度，多云
    A->>L: 把函数结果作为 tool 消息回传
    L-->>A: 苏州今天多云，气温 32°C
    A->>U: 返回自然语言答案
</div>

注意应用向 LLM 发了**两次**请求：**同一轮对话里模型被调用了两次**。第一次决定"调什么"，第二次基于结果"说人话"。这就是所谓的 **tool-use loop（工具调用循环）**。

### 2.3 具体例子：查天气

先定义工具。这个 JSON Schema 就是给模型看的"空白表单"：

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "查询指定城市的当前天气。当用户询问天气、气温、是否下雨时使用。",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "城市名称，英文拼写，例如 Suzhou、Beijing"
                    },
                    "unit": {
                        "type": "string",
                        "enum": ["celsius", "fahrenheit"],
                        "description": "温度单位，默认摄氏度"
                    }
                },
                "required": ["city"]
            }
        }
    }
]
```

然后是完整的调用循环：

```python
import json
from openai import OpenAI

client = OpenAI()

def get_weather(city: str, unit: str = "celsius") -> dict:
    """这才是真正干活的函数——去调气象 API。"""
    return {"city": city, "temp": 32, "unit": unit, "desc": "多云"}

messages = [{"role": "user", "content": "苏州今天天气怎么样？"}]

# 第 1 次调用：让模型决定要不要用工具
resp = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
)
msg = resp.choices[0].message
messages.append(msg)

# 模型决定调用工具，我们来执行
if msg.tool_calls:
    for call in msg.tool_calls:
        args = json.loads(call.function.arguments)
        result = get_weather(**args)          # ← 真正的执行发生在这里
        messages.append({
            "role": "tool",
            "tool_call_id": call.id,
            "content": json.dumps(result, ensure_ascii=False),
        })

# 第 2 次调用：让模型基于结果组织语言
final = client.chat.completions.create(model="gpt-4o", messages=messages)
print(final.choices[0].message.content)
# → 苏州今天多云，气温 32°C，注意防暑。
```

### 2.4 三个容易踩的坑

1. **`description` 写得好不好，直接决定模型调不调用。** 这段描述是模型判断"什么时候该用这个工具"的唯一依据。写成"天气工具"就很容易被漏掉，写成"当用户询问天气、气温、是否下雨时使用"命中率高得多。
2. **工具数量不是越多越好。** 每个工具定义都要占 prompt 的 token，几十个工具塞进去，模型选错的概率会明显上升，成本也一起涨。
3. **参数不可信。** 模型有可能生成不符合 schema 的参数，或者幻觉出一个不存在的城市。**执行前必须校验**。

---

## 三、MCP：把工具做成"可插拔的 USB 设备"

### 3.1 为什么需要一个协议

Function Calling 好用，但有个致命的工程问题：**每个应用都要为每个工具单独写一遍胶水代码。**

你想让 Cursor 连 GitHub，得写一份适配；想让 Claude Desktop 连 GitHub，再写一份；想让自研 Agent 连 GitHub，再写一份。M 个应用 × N 个工具 = **M×N 份重复劳动**。

MCP（Model Context Protocol）是 Anthropic 在 2024 年底开源的协议，用一层标准接口把它拆成 **M+N**：

<div class="mermaid">
graph LR
    subgraph BEFORE["❌ 没有 MCP：M×N 份适配"]
        A1[Cursor] --- T1[GitHub]
        A1 --- T2[Postgres]
        A1 --- T3[Figma]
        A2[Claude] --- T1
        A2 --- T2
        A2 --- T3
        A3[自研 Agent] --- T1
        A3 --- T2
        A3 --- T3
    end

    style A1 fill:#ffd6e0,color:#3d3556
    style A2 fill:#ffd6e0,color:#3d3556
    style A3 fill:#ffd6e0,color:#3d3556
    style T1 fill:#ffdac1,color:#3d3556
    style T2 fill:#ffdac1,color:#3d3556
    style T3 fill:#ffdac1,color:#3d3556
</div>

<div class="mermaid">
graph LR
    subgraph AFTER["✅ 有了 MCP：M+N 份适配"]
        B1[Cursor] --> P((MCP 协议))
        B2[Claude] --> P
        B3[Hermes] --> P
        B4[自研 Agent] --> P
        P --> S1[GitHub<br/>MCP Server]
        P --> S2[Postgres<br/>MCP Server]
        P --> S3[Figma<br/>MCP Server]
    end

    style B1 fill:#b5ead7,color:#3d3556
    style B2 fill:#b5ead7,color:#3d3556
    style B3 fill:#b5ead7,color:#3d3556
    style B4 fill:#b5ead7,color:#3d3556
    style P fill:#c7ceea,color:#3d3556
    style S1 fill:#bee3db,color:#3d3556
    style S2 fill:#bee3db,color:#3d3556
    style S3 fill:#bee3db,color:#3d3556
</div>

一个很贴切的类比：**MCP 之于 AI 工具，就像 USB-C 之于外设**。设备厂商按标准做接口，主机厂商按标准做插槽，两边不需要互相认识。

### 3.2 MCP 提供的三类能力

很多人以为 MCP 只是"工具协议"，其实它定义了三种原语：

| 原语 | 含义 | 谁来触发 | 典型例子 |
| --- | --- | --- | --- |
| **Tools（工具）** | 可执行的动作，有副作用 | 模型自主决定调用 | `create_issue`、`run_query` |
| **Resources（资源）** | 只读的上下文数据 | 应用/用户选择注入 | 一个文件的内容、一张表的 schema |
| **Prompts（提示词模板）** | 预置的提示词模板 | 用户显式触发 | "帮我 review 这个 PR"的标准模板 |

日常用得最多的是 Tools，但 Resources 在"把数据库表结构喂给模型"这种场景下非常好用。

### 3.3 具体例子：三十行写一个 MCP Server

用官方的 Python SDK（`pip install "mcp[cli]"`），把上面那个天气函数变成一个标准 MCP Server：

```python
# weather_server.py
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("weather")

@mcp.tool()
def get_weather(city: str, unit: str = "celsius") -> dict:
    """查询指定城市的当前天气。当用户询问天气、气温、是否下雨时使用。

    Args:
        city: 城市名称，英文拼写，例如 Suzhou
        unit: 温度单位，celsius 或 fahrenheit
    """
    return {"city": city, "temp": 32, "unit": unit, "desc": "多云"}

@mcp.resource("weather://cities")
def supported_cities() -> str:
    """返回支持查询的城市清单。"""
    return "Suzhou, Shanghai, Beijing, Shenzhen"

if __name__ == "__main__":
    mcp.run()   # 默认走 stdio 传输
```

注意几个细节：

- **函数签名的类型标注会被自动转成 JSON Schema**，你不用手写那一大坨 `parameters`。
- **docstring 会变成工具的 `description`**，也就是模型判断"该不该调"的依据——所以 docstring 要认真写。
- 这个文件写完之后，**Cursor、Claude Desktop、Hermes、任何支持 MCP 的客户端都能直接用**，一行适配代码都不用改。

### 3.4 两种传输方式

| 传输 | 进程位置 | 适用场景 | 配置方式 |
| --- | --- | --- | --- |
| **stdio** | 客户端在本地把 server 当子进程拉起，走标准输入输出 | 个人本地工具、需要访问本机文件 | 给一个 `command` + `args` |
| **HTTP / SSE** | server 独立部署，可远程 | 团队共享、需要鉴权、多人同时用 | 给一个 `url` + `headers` |

初学者从 stdio 开始就够了，`npx`/`uvx` 一条命令就能拉起社区现成的 server。

---

## 四、Skills：给 Agent 一本"岗位 SOP 手册"

### 4.1 它和前两者的本质区别

Function Calling 和 MCP 解决的都是**"能力"**问题——Agent 能不能读文件、能不能查数据库。

但你很快会遇到另一类问题：**Agent 有能力，却做得不专业。**

比如你让它"写个发布说明"，它确实能读 git log、能写文件，但格式每次都不一样，该带的版本号忘了带，该问的人没问。**这不是能力问题，是流程和规范问题。**

Skills 就是为此设计的：**一个 Markdown 文件，把"这类任务的正确做法"写下来，让 Agent 在遇到相关任务时自动加载。**

它不引入任何新工具，它引入的是**知识和纪律**。

### 4.2 渐进式披露：为什么是 Markdown 而不是代码

Skills 的设计核心是 **progressive disclosure（渐进式披露）**，分三级加载：

<div class="mermaid">
graph TB
    L1["第 1 级：始终在上下文里<br/>只有 name + description<br/>约几十个 token"] --> L2
    L2["第 2 级：判断相关时加载<br/>SKILL.md 正文<br/>建议 500 行以内"] --> L3
    L3["第 3 级：真正需要时才读<br/>references/ 详细文档<br/>scripts/ 可执行脚本"]

    style L1 fill:#b5ead7,color:#3d3556
    style L2 fill:#ffdac1,color:#3d3556
    style L3 fill:#c7ceea,color:#3d3556
</div>

这个设计解决了一个很现实的矛盾：你想给 Agent 塞很多领域知识，但上下文窗口有限、token 要钱。渐进式披露让**平时只付几十 token 的"目录费"，用到时才付"正文费"**。

所以写 Skill 有条铁律：**`description` 字段是永久成本，正文是按需成本。** description 要写得精准（包含"做什么"和"什么时候用"），正文可以详细。

### 4.3 具体例子：一个发布周报的 Skill

目录结构：

```
.cursor/skills/
└── weekly-report/
    ├── SKILL.md              # 必需：主指令
    ├── references/
    │   └── jira-jql.md       # 按需加载：JQL 查询语法速查
    └── assets/
        └── template.md       # 周报模板
```

`SKILL.md` 内容：

````markdown
---
name: weekly-report
description: 从 Jira 拉取本周工单并生成团队周报。当用户提到周报、周会材料、本周进展汇总时使用。
---

# 团队周报生成

## 何时使用

用户说"生成周报""整理本周进展""周会材料"时触发。

## 步骤

1. **确认周期**：默认取本周一 00:00 到当前时间。跨周时先向用户确认。
2. **拉取数据**：用 Jira MCP 的 `jira_search` 工具执行 JQL：

   ```
   project = PLAT AND updated >= startOfWeek() ORDER BY status
   ```

   常用 JQL 写法见 [references/jira-jql.md](references/jira-jql.md)。
3. **分类归档**：按 `已完成 / 进行中 / 受阻` 三档分组。受阻项必须写明阻塞原因。
4. **套用模板**：读取 `assets/template.md`，按模板填充，不要自创格式。
5. **交付**：写入 `reports/YYYY-WW.md`，然后把摘要贴回聊天。

## 硬性规范

- 每条工单格式固定为：`[PLAT-1234] 标题 —— 负责人`
- 受阻项排在最前面，且用 `⚠️` 标记
- 不要臆测进度百分比，Jira 里没有的数据就写"未填写"
- 生成后**不要**自动发送到任何群，等用户确认
````

看明白了吗？这里面**没有一行代码定义新工具**。`jira_search` 是 MCP 提供的，写文件是内置工具提供的。Skill 做的事情是：**把这些既有能力，编排成一套有规范、可复现的流程。**

### 4.4 Skills 的格式正在成为标准

`SKILL.md` 这个格式最早由 Anthropic 提出，现在已经演化成一个跨平台的开放标准（agentskills.io），Cursor、Claude Code、Codex CLI、Gemini CLI、Hermes Agent 都能识别。**同一个 skill 目录，拷到不同工具里基本都能直接用**，这是它比各家私有配置格式更有价值的地方。

---

## 五、三者的区别：一张表说清

### 5.1 全维度对比

| 对比维度 | Function Calling | MCP | Skills |
| --- | --- | --- | --- |
| **层次** | 模型能力层 | 传输/接口层 | 提示词/知识层 |
| **本质是什么** | 模型输出结构化 JSON 的能力 | 一套 client-server 通信协议 | 一个 Markdown 文件 |
| **谁定义** | 应用开发者写 JSON Schema | MCP Server 作者 | 使用者/团队自己写 |
| **谁执行** | 你的应用代码 | MCP Server 进程 | 没有"执行"，它只是指令 |
| **是否增加新能力** | ✅ 是（把外部函数接进来） | ✅ 是（把外部系统接进来） | ❌ 否（只改变做事方式） |
| **跨应用复用** | ❌ 每个应用要重写 | ✅ 一次编写处处可用 | ✅ 目录拷贝即可 |
| **是否需要写代码** | ✅ 需要 | ✅ 需要（写 server） | ❌ 不需要，写 Markdown |
| **上下文开销** | 每个工具的 schema 常驻 | 同上（MCP 工具最终也变成 function schema） | 平时只有 description，正文按需 |
| **典型产物** | 一段 `tools=[...]` 定义 | 一个可执行的 server 程序 | 一个 `SKILL.md` 文件 |
| **失效表现** | 模型不调用/参数错 | 连不上/工具发现失败 | Agent 不按规范做事 |

### 5.2 关键洞察：MCP 最终也变成 Function Calling

这一点很多人没想明白，但它是理解三者关系的钥匙：

<div class="mermaid">
graph LR
    A[MCP Server<br/>暴露 tools] -->|客户端调用 list_tools| B[客户端拿到工具描述]
    B -->|转换成 JSON Schema| C[塞进 LLM 的 tools 参数]
    C -->|LLM 输出调用请求| D[Function Calling]
    D -->|客户端转发| E[MCP Server 执行]

    style A fill:#bee3db,color:#3d3556
    style B fill:#e2f0cb,color:#3d3556
    style C fill:#ffdac1,color:#3d3556
    style D fill:#c7ceea,color:#3d3556
    style E fill:#bee3db,color:#3d3556
</div>

**MCP 不是 Function Calling 的替代品，而是 Function Calling 的"上游供货商"。** 模型侧看到的永远是一份 function schema 清单，它根本不知道某个工具是本地硬编码的还是从 MCP server 发现的。

所以"该用 Function Calling 还是 MCP"是个伪问题。真正的问题是：**这个工具只有我这个应用用，还是想让所有 AI 客户端都能用？** 前者直接硬编码，后者做成 MCP Server。

### 5.3 三个常见误区

| 误区 | 纠正 |
| --- | --- |
| "MCP 比 Function Calling 更先进，应该淘汰后者" | 两者是上下游关系。MCP 工具最终仍然通过 function calling 被模型调用 |
| "Skills 就是把 prompt 存成文件" | 本质上是，但关键在于**按需加载机制**和**跨工具标准化**，这让它可维护、可共享 |
| "有了 Skills 就不用 MCP 了" | Skill 没有执行能力。让 Agent 查数据库，光靠 Markdown 是查不出来的 |

---

## 六、能不能搭配使用？——不仅能，而且这才是正确姿势

### 6.1 它们本来就是为叠加设计的

三者互不冲突，因为它们各管一段。一个成熟的 Agent 配置通常长这样：

<div class="mermaid">
graph TB
    subgraph K["📖 Skills：定义流程与规范"]
        K1[incident-response/SKILL.md]
        K2[weekly-report/SKILL.md]
        K3[code-review/SKILL.md]
    end

    subgraph M["🔌 MCP：接入外部系统"]
        M1[Jira MCP]
        M2[Grafana MCP]
        M3[GitHub MCP]
    end

    subgraph N["🔧 内置工具：本地操作"]
        N1[terminal]
        N2[read_file / write_file]
        N3[web_search]
    end

    K1 -.引用.-> M2
    K1 -.引用.-> M1
    K2 -.引用.-> M1
    K2 -.引用.-> N2
    K3 -.引用.-> M3
    K3 -.引用.-> N1

    style K1 fill:#ffdac1,color:#3d3556
    style K2 fill:#ffdac1,color:#3d3556
    style K3 fill:#ffdac1,color:#3d3556
    style M1 fill:#bee3db,color:#3d3556
    style M2 fill:#bee3db,color:#3d3556
    style M3 fill:#bee3db,color:#3d3556
    style N1 fill:#b5ead7,color:#3d3556
    style N2 fill:#b5ead7,color:#3d3556
    style N3 fill:#b5ead7,color:#3d3556
</div>

虚线的意思是：**Skill 在文字里"点名"要用哪些 MCP 工具**。这是最常见的组合方式。

### 6.2 完整实例：线上故障响应

假设你想让 Agent 帮忙处理告警。三层各干各的：

**第一层 —— MCP（提供能力）**

```yaml
# 接入三个外部系统
grafana:   查询指标、拉取告警详情
jira:      创建/更新工单
github:    查最近的提交和 PR
```

**第二层 —— Function Calling（自动发生）**

客户端启动时连上这三个 MCP Server，发现大约 20 个工具，转成 JSON Schema 塞进模型的 `tools` 参数。这一步**你什么都不用做**，是客户端自动完成的。

**第三层 —— Skill（编排流程）**

```markdown
---
name: incident-response
description: 处理线上告警的标准流程，包含定位、止血、记录三个阶段。当用户提到告警、故障、P1、线上问题时使用。
---

# 线上故障响应

## 阶段一：定位（不要跳过）

1. 用 `grafana_get_alert` 拉取告警详情，记录**触发时间**
2. 用 `grafana_query_range` 查触发时间**前后 30 分钟**的关键指标：
   QPS、P99 延迟、错误率、CPU、内存
3. 用 `github_list_commits` 查触发时间**前 2 小时**内的部署记录
4. 输出一份"疑似原因"清单，按可能性排序

## 阶段二：止血（必须人工确认）

⚠️ **本阶段任何操作都必须先向用户确认，禁止自动执行。**

- 如果定位到是最近发布导致 → 建议回滚，给出具体命令但**不要执行**
- 如果是容量问题 → 建议扩容，给出具体参数

## 阶段三：记录

1. 用 `jira_create_issue` 建工单，type 固定为 `Incident`
2. 标题格式：`[P{级别}] {服务名} - {现象}`
3. 描述里必须包含：时间线、影响面、根因、后续 action
4. 把工单链接贴回聊天
```

**效果对比**：

| 对比项 | 只有 MCP | MCP + Skill |
| --- | --- | --- |
| 能不能查到数据 | ✅ 能 | ✅ 能 |
| 会不会漏查部署记录 | ❓ 看运气 | ✅ 流程里写死了 |
| 会不会擅自回滚 | ⚠️ 有风险 | ✅ 明确禁止 |
| 工单格式统一吗 | ❌ 每次都不一样 | ✅ 模板固定 |
| 新人接手能复现吗 | ❌ 不能 | ✅ 流程即文档 |

### 6.3 什么时候用哪个：决策树

<div class="mermaid">
graph TB
    Q1{Agent 缺的是<br/>能力还是规范?}
    Q1 -->|缺能力| Q2{这个能力<br/>别的工具也想用?}
    Q1 -->|缺规范| SK[写 Skill<br/>SKILL.md]

    Q2 -->|只有我自己用| FC[直接写 Function<br/>硬编码工具]
    Q2 -->|想跨工具复用| Q3{已经有现成的<br/>MCP Server 吗?}

    Q3 -->|有| USE[直接配置使用]
    Q3 -->|没有| BUILD[自己写一个<br/>MCP Server]

    SK -.经常需要.-> Q2

    style Q1 fill:#c7ceea,color:#3d3556
    style Q2 fill:#c7ceea,color:#3d3556
    style Q3 fill:#c7ceea,color:#3d3556
    style SK fill:#ffdac1,color:#3d3556
    style FC fill:#b5ead7,color:#3d3556
    style USE fill:#bee3db,color:#3d3556
    style BUILD fill:#bee3db,color:#3d3556
</div>

一条经验法则：**先用 Skill 试。** 很多你以为需要开发工具的场景，其实只是没把流程讲清楚。Skill 的成本是写一个 Markdown 文件，MCP Server 的成本是写、测、部署、维护一个程序。

---

## 七、在 Cursor 里怎么用

> 以下配置对应 Cursor 3.6 前后的版本（2026 年年中）。Cursor 的审批机制在 2026 年上半年改过两次，看到旧教程里的 "YOLO mode" 请自动翻译成现在的 Run Modes。

### 7.1 Function Calling：内置工具 + 审批模式

Cursor 的内置工具是**开箱即用**的，你不需要定义任何 function schema。文档里列出的工具类别包括：

| 工具 | 用途 |
| --- | --- |
| Search files and folders | 按名称搜索、读目录结构、找关键字（用的是自研的 Instant Grep） |
| Read files / Edit files | 读写文件，支持图片格式给视觉模型 |
| Run shell commands | 执行终端命令 |
| Web | 生成搜索词并联网搜索 |
| Browser | 截图、点击、导航 |
| Fetch Rules | 按需拉取规则 |
| Image generation | 生成图片，默认存到项目的 `assets/` |

**你需要管的是"什么能自动跑"**。路径：**Settings → Agents → Approvals & Execution**，三种模式：

| 模式 | 行为 |
| --- | --- |
| **Auto-review**（推荐默认） | 允许清单里的直接跑；其他 shell 命令尽量在沙箱里跑；跑不了沙箱的交给分类器判断 |
| **Allowlist** | 只有允许清单里的自动跑，其余都问你 |
| **Run Everything** | 全部自动跑（谨慎） |

想精细控制，写 `~/.cursor/permissions.json` 或 `<项目>/.cursor/permissions.json`：

```json
{
  "mcpAllowlist": ["github:*", "linear:list_issues"],
  "terminalAllowlist": ["git", "npm", "cargo build"],
  "autoRun": {
    "allow_instructions": [
      "对 ./dist 下构建产物的只读检查可以直接执行。"
    ],
    "block_instructions": [
      "所有 AWS CLI 命令都必须先经过审批。"
    ]
  }
}
```

MCP 条目用 `server:tool` 格式，支持 `*` 通配、大小写不敏感；终端条目是**大小写敏感的前缀匹配**，`git` 能匹配 `git status` 但匹配不到 `gitk`。

顺带一句文档里的原话，值得记住：**"Auto-review is not a security boundary."** 它是效率工具，不是安全边界。

### 7.2 MCP：`.cursor/mcp.json`

两个位置，都用同一套格式，**同名 server 以项目级为准**：

| 路径 | 作用域 |
| --- | --- |
| `.cursor/mcp.json` | 当前项目 |
| `~/.cursor/mcp.json` | 全局，所有项目可用 |

本地 stdio 配置：

```json
{
  "mcpServers": {
    "weather": {
      "command": "uvx",
      "args": ["--from", ".", "weather-server"],
      "env": {
        "API_KEY": "${env:WEATHER_API_KEY}"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "${workspaceFolder}"]
    }
  }
}
```

远程 HTTP / SSE 配置：

```json
{
  "mcpServers": {
    "company-api": {
      "url": "https://mcp.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${env:COMPANY_TOKEN}"
      }
    }
  }
}
```

几个实用细节：

- **变量插值**在 `command`、`args`、`env`、`url`、`headers` 里都生效，支持 `${env:NAME}`、`${userHome}`、`${workspaceFolder}`、`${pathSeparator}`。**密钥请一律用 `${env:...}`，不要硬编码进 json 提交到仓库。**
- **开关**：侧边栏 **Customize → MCPs**，每个 server 有个开关。文档明确说关掉不用的 server 有助于"减少工具杂讯"——工具太多确实会拖累模型的选择准确率。
- **一键安装**：Cursor Marketplace（`cursor.com/marketplace`）里点 **Add to Cursor**，OAuth 也一并走完。
- **排错**：输出面板（Windows 下 `Ctrl+Shift+U`）里选 **MCP Logs**。

### 7.3 Skills：`SKILL.md` 放对地方就行

Cursor 会扫描这四个目录（前两个是项目级，后两个是全局）：

```
.agents/skills/          .cursor/skills/
~/.agents/skills/        ~/.cursor/skills/
```

另外还兼容 `.claude/skills/` 和 `.codex/skills/`，所以从别的生态搬过来的 skill 大多能直接用。

`SKILL.md` 的 frontmatter 字段就这几个：

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `name` | ✅ | 小写字母、数字、连字符，**必须和所在文件夹同名** |
| `description` | ✅ | 做什么 + 什么时候用，Agent 靠它判断相关性 |
| `paths` | ❌ | glob 模式，只在处理匹配的文件时才加载 |
| `disable-model-invocation` | ❌ | 设为 `true` 则只有你手动打 `/skill-name` 才加载 |
| `metadata` | ❌ | 任意键值对 |

（`globs` 是老字段，仍然兼容，新写的用 `paths`。）

一个用 `paths` 做范围限定的例子：

```markdown
---
name: react-component-patterns
description: 本仓库编写 React 组件的约定。
paths:
  - "**/*.tsx"
  - "packages/ui/**/*.ts"
---

# React 组件规范
...
```

**调用方式有三种**：Agent 根据 `description` 自动判断加载、你打 `/skill-name` 显式调用、你用 `@skill-name` 当作上下文附加。想让它变成纯粹的斜杠命令，加 `disable-model-invocation: true`。

还有个很省心的机制：**嵌套目录自动限定作用域**。放在 `apps/web/.cursor/skills/` 下的 skill，只有 Agent 在处理 `apps/web/` 下的文件时才会浮现出来，你不用手写 `paths`。

### 7.4 Skills 和 Rules 是两个东西

这是 Cursor 用户最容易混的一对。官方帮助页的区分是：

- **Rules**（`.cursor/rules/*.mdc`、`AGENTS.md`）：**短的编码准则和约束**，作为上下文加进每次（或匹配的）对话。
- **Skills**（`SKILL.md`）：**多步骤的工作流和规程**，按需用 `/skill-name` 或 `@skill-name` 调起。

一个提醒：`.cursor/rules` 目录里的 `.md` 文件**会被忽略**，项目规则必须是 `.mdc`——因为 `.md` 没地方写 `description`、`globs`、`alwaysApply` 这些 frontmatter。

如果你以前写了一堆 rules，Cursor 内置了 `/migrate-to-skills` 帮你迁移。

### 7.5 三者合体的样子

在 Cursor 里，一个配好的项目大概长这样：

```
my-project/
├── .cursor/
│   ├── mcp.json                    # ← MCP：接入 Jira / Grafana
│   ├── permissions.json            # ← Function Calling：哪些工具能自动跑
│   ├── rules/
│   │   └── coding-style.mdc        # ← Rules：始终生效的编码约束
│   └── skills/
│       ├── incident-response/
│       │   └── SKILL.md            # ← Skills：故障响应流程
│       └── weekly-report/
│           ├── SKILL.md
│           └── assets/template.md
└── AGENTS.md                       # ← 项目总体说明
```

然后你只需要说一句"帮我处理下这个告警"，Agent 会自己匹配到 `incident-response` skill，按流程去调 Grafana 和 Jira 的 MCP 工具。

---

## 八、在 Hermes Agent 里怎么用

[Hermes Agent](https://github.com/NousResearch/hermes-agent) 是 Nous Research 开源的自我改进型个人 Agent（我之前写过一篇[基于源码的解析]({{ '/2026/04/21/hermes-framework/' | relative_url }})）。它对这三样东西的支持很完整，但配置路径和 Cursor 完全不同。

### 8.1 Function Calling：`tools/` 目录 + toolsets

Hermes 的工具都在仓库的 `tools/` 目录下，一个工具一个文件，由 `tools/registry.py` 统一注册。核心的几个：

| 文件 | 提供的能力 |
| --- | --- |
| `terminal_tool.py` | 终端执行，支持 local / docker / ssh / daytona / singularity / modal 六种后端 |
| `file_tools.py` | read / write / search / patch |
| `web_tools.py` | 搜索与抓取 |
| `browser_tool.py` | 浏览器自动化 |
| `delegate_tool.py` | 派发 subagent，可多路并行 |
| `mcp_tool.py` | MCP 客户端 |
| `approval.py` | 危险命令探测（`rm -rf`、`curl \| sh` 之类），配合人工确认 |

控制哪些工具启用：

```bash
hermes tools           # 交互式启用/禁用内置工具
```

有一点要特别注意：**用自己部署的模型时，一定要选带 function call / tools 支持的版本**。比如 Qwen 系列要用 Instruct 版，否则 `tools/` 里的 schema 派发会整个失效，Agent 会退化成一个只会聊天的 chatbot。

### 8.2 MCP：`~/.hermes/config.yaml` 里的 `mcp_servers`

先装 SDK（可选依赖，不装的话 MCP 支持会静默禁用）：

```bash
pip install mcp
```

然后在 `~/.hermes/config.yaml` 里加 `mcp_servers` 段：

```yaml
mcp_servers:
  # stdio 传输
  time:
    command: "uvx"
    args: ["mcp-server-time"]

  filesystem:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/documents"]
    timeout: 30

  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "ghp_xxxx"

  # HTTP 传输
  company_api:
    url: "https://mcp.internal.company.com/mcp"
    headers:
      Authorization: "Bearer sk-xxxx"
    timeout: 180
    connect_timeout: 30
```

全部可选项：

| 选项 | 类型 | 默认 | 说明 |
| --- | --- | --- | --- |
| `command` | string | — | stdio 必填，要执行的命令 |
| `args` | list | `[]` | 命令参数 |
| `env` | dict | `{}` | 传给子进程的额外环境变量 |
| `url` | string | — | HTTP 必填，server 地址 |
| `headers` | dict | `{}` | 每次请求带上的 HTTP 头 |
| `timeout` | int | `120` | 单次工具调用超时（秒） |
| `connect_timeout` | int | `60` | 初次连接与工具发现的超时 |

`command` 和 `url` 二选一，不能同时给。

**几个 Hermes 特有的行为，用之前最好知道：**

1. **工具命名带前缀**：注册进来的工具名是 `mcp_{server名}_{工具名}`，名字里的连字符和点会被换成下划线。所以 `github` server 的 `list-issues` 会变成 `mcp_github_list_issues`。看不到工具时，先按这个模式找找。
2. **环境变量是过滤过的**。Hermes **不会**把你的完整 shell 环境传给 MCP 子进程，只继承 `PATH`、`HOME`、`USER`、`LANG`、`TERM`、`SHELL`、`TMPDIR` 和 `XDG_*`。**其他所有变量（包括 API key）必须在 `env` 里显式声明才会传进去。** 这是有意的防泄漏设计，别当成 bug。
3. **改完配置要重启**，目前没有热加载。
4. **报错信息里的凭据会被自动打码**（`ghp_`、`sk-`、Bearer token、`password=` 之类的模式），不会原样喂给 LLM。
5. **反向也支持**：`mcp_serve.py` 能把 Hermes 自己暴露成一个 MCP server，让别的客户端来调它。

### 8.3 Skills：`~/.hermes/skills/`

Hermes 的 skill 按**类目（category）**组织，比 Cursor 多一层：

```
~/.hermes/skills/
├── github/
│   ├── DESCRIPTION.md         # 描述整个类目是干什么的
│   └── <具体skill>/SKILL.md
├── mcp/
│   ├── DESCRIPTION.md
│   └── native-mcp/SKILL.md
└── my-custom-skill/
    └── SKILL.md               # 也可以不分类，直接放
```

Hermes 的 `SKILL.md` frontmatter 比 Cursor 多几个字段，我本机的一个 skill 是这样写的：

```markdown
---
name: weather-query-bosch-proxy
description: "Query weather via wttr.in through Bosch NTLM proxy"
aliases:
  - 天气查询
  - 查询天气
categories:
  - productivity
  - utilities
---

# Weather Query via Bosch NTLM Proxy

## Quick Command

...
```

`aliases` 是 Hermes 特有的，**支持中文别名**，对中文用户挺友好。

管理命令：

```bash
hermes skills                    # 交互式启用/禁用
```

在会话里则用斜杠命令：

```
/skills                          # 浏览、搜索、安装第三方 skill
```

Hermes 内置了多个生态的 skill 索引（Claude 市场、LobeHub、OpenAI 等），可以直接 `browse / search / install` 装第三方的。另外仓库里还有 `optional-skills/`，是官方维护但默认不装的（因为依赖外部 API key 或体积大），需要 `hermes skills install <名字>` 手动启用。

有个实现细节挺有意思：**skill 是作为"用户消息"注入的，不是塞进 system prompt**。源码注释里写得很直白——这么做是为了让 Anthropic 的 prompt caching 继续命中。很实际的工程取舍。

### 8.4 Cursor vs Hermes：配置速查

| 对比项 | Cursor | Hermes Agent |
| --- | --- | --- |
| **MCP 配置文件** | `.cursor/mcp.json` / `~/.cursor/mcp.json` | `~/.hermes/config.yaml` |
| **MCP 配置格式** | JSON，`mcpServers` 键 | YAML，`mcp_servers` 键 |
| **MCP 工具命名** | 原名 | `mcp_{server}_{tool}` 前缀 |
| **Skills 目录** | `.cursor/skills/`、`.agents/skills/`（含全局版） | `~/.hermes/skills/`（可再分类目） |
| **Skill 额外字段** | `paths`、`disable-model-invocation` | `aliases`、`categories`、`version` |
| **工具开关** | Customize → MCPs / `permissions.json` | `hermes tools` / `hermes skills` |
| **审批机制** | Run Modes（Auto-review / Allowlist / Run Everything） | `approvals.mode` + `tools/approval.py` 危险命令探测 |
| **第三方 skill 安装** | Customize → Rules → Remote Rule (Github) | `/skills` 或 `hermes skills install` |

---

## 九、GitHub 上值得关注的仓库

下面这些仓库我都通过 GitHub API 核对过，星标数为 **2026 年 7 月**的快照。

### 9.1 MCP 官方与基础设施

| 仓库 | Stars | 说明 |
| --- | --- | --- |
| [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) | 89.0k | **官方参考实现集合**，filesystem、git、fetch、memory 等。想学 MCP 从这里读源码 |
| [modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk) | 23.7k | 官方 Python SDK，`FastMCP` 就在里面 |
| [modelcontextprotocol/typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk) | 13.0k | 官方 TypeScript SDK |
| [modelcontextprotocol/inspector](https://github.com/modelcontextprotocol/inspector) | 10.5k | **可视化调试工具**，写 server 时的必备品，能直接看到工具列表和调用结果 |
| [PrefectHQ/fastmcp](https://github.com/PrefectHQ/fastmcp) | 26.9k | 更 Pythonic 的 MCP 框架，装饰器风格 |
| [mcp-use/mcp-use](https://github.com/mcp-use/mcp-use) | 10.4k | 全栈 MCP 框架，同时做 MCP App 和 MCP Server |

### 9.2 现成可用的 MCP Server

| 仓库 | Stars | 说明 |
| --- | --- | --- |
| [punkpeye/awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) | 91.5k | **MCP server 大全**，找工具先来这里 |
| [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) | 35.6k | 微软官方，浏览器自动化。让 Agent 真正能点网页 |
| [github/github-mcp-server](https://github.com/github/github-mcp-server) | 31.8k | GitHub 官方，issue / PR / 仓库操作 |
| [googleapis/mcp-toolbox](https://github.com/googleapis/mcp-toolbox) | 16.0k | 数据库 MCP，Google 出品 |
| [GLips/Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP) | 15.5k | 把 Figma 设计稿信息喂给 Cursor，设计转代码常用 |
| [awslabs/mcp](https://github.com/awslabs/mcp) | 9.5k | AWS 官方的一套 MCP Server |
| [hangwin/mcp-chrome](https://github.com/hangwin/mcp-chrome) | 12.2k | 基于 Chrome 扩展，能操作你**现有的**浏览器（带登录态） |

### 9.3 Skills 仓库

| 仓库 | Stars | 说明 |
| --- | --- | --- |
| [anthropics/skills](https://github.com/anthropics/skills) | 164.6k | **官方 Agent Skills 仓库**，格式标准的源头，先读这个 |
| [obra/superpowers](https://github.com/obra/superpowers) | 262.2k | 一整套 agentic skills 框架 + 软件开发方法论，社区热度最高 |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | 80.7k | Addy Osmani 的工程向 skills，质量很高 |
| [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 29.1k | 1000+ skill 精选集，明确标注兼容 Cursor / Claude Code / Codex / Gemini CLI |
| [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | 29.5k | Vercel 官方 skills 集合 |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 71.1k | Claude Skills 资源精选 |
| [github/awesome-copilot](https://github.com/github/awesome-copilot) | 37.1k | GitHub 官方，社区贡献的 instructions / agents / skills |
| [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | 32.0k | 156 个科研向 skill，覆盖生物、化学、医药 |
| [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) | 43.5k | 教 Agent 操作 Obsidian，个人知识管理场景 |

### 9.4 同时用到 MCP + Skills 的项目（最值得读）

想看"三者怎么配合"，读这几个仓库最直观：

| 仓库 | Stars | 为什么值得看 |
| --- | --- | --- |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | 221.5k | 内置 MCP 客户端（`tools/mcp_tool.py`）+ 25 个类目的 skill 体系 + 自我改进闭环，本文第八节的配置全部来自它 |
| [danny-avila/LibreChat](https://github.com/danny-avila/LibreChat) | 41.4k | 开源 ChatGPT 替代品，Agents + MCP + Skills 全都有，前后端完整 |
| [zylon-ai/private-gpt](https://github.com/zylon-ai/private-gpt) | 57.4k | 私有化部署的完整 API 层：RAG、skills、tools、MCP 一应俱全 |
| [sickn33/agentic-awesome-skills](https://github.com/sickn33/agentic-awesome-skills) | 44.0k | 1987+ skill 的目录服务，**本身就带一个 local MCP** ——用 MCP 来分发 skill，思路很妙 |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | 46.2k | 中文社区的老牌项目（原 chatgpt-on-wechat），工具 + skill + 记忆的完整实现 |
| [activepieces/activepieces](https://github.com/activepieces/activepieces) | 23.4k | 约 400 个 MCP Server 的自动化平台，看它怎么做工作流编排 |

> **上手建议**：先 clone [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) 挑最简单的 `fetch` 读一遍，再 clone [anthropics/skills](https://github.com/anthropics/skills) 挑一个 skill 读一遍。两个仓库加起来两小时，比看十篇教程管用。

---

## 十、总结

### 10.1 三句话记住区别

- **Function Calling** 是**模型的能力**——它让 LLM 能输出"我要调这个函数、传这些参数"。没有它，一切免谈。
- **MCP** 是**工具的接口**——它让一个工具写一次，所有 AI 客户端都能用。它最终还是喂给 function calling 的。
- **Skills** 是**Agent 的手册**——它不给新能力，但决定 Agent 做事专不专业、稳不稳定。

### 10.2 上手路线

<div class="mermaid">
graph LR
    S1["1. 用现成的<br/>Cursor / Hermes<br/>体验内置工具"] --> S2["2. 配一个 MCP<br/>从 filesystem<br/>或 github 开始"]
    S2 --> S3["3. 写第一个 Skill<br/>把你最常重复的<br/>流程写成 SKILL.md"]
    S3 --> S4["4. 写自己的<br/>MCP Server<br/>接内部系统"]
    S4 --> S5["5. 组合<br/>Skill 编排<br/>多个 MCP 工具"]

    style S1 fill:#b5ead7,color:#3d3556
    style S2 fill:#bee3db,color:#3d3556
    style S3 fill:#ffdac1,color:#3d3556
    style S4 fill:#c7ceea,color:#3d3556
    style S5 fill:#e2f0cb,color:#3d3556
</div>

### 10.3 几条实践经验

1. **先写 Skill，再造工具。** 很多"Agent 做不好"的问题，本质是你没把要求说清楚，而不是缺工具。写 Markdown 的成本远低于写、测、部署、维护一个 MCP Server。
2. **工具不是越多越好。** MCP Server 接得越多，塞进 prompt 的工具描述就越长，模型选错的概率和 token 成本一起涨。Cursor 文档专门提到关掉不用的 server 可以"减少工具杂讯"，这是真话。
3. **`description` 是最值钱的一行。** 无论是 function 的 description、MCP 工具的 docstring，还是 SKILL.md 的 description——它们都是模型判断"要不要用"的唯一依据。这一行值得反复打磨。
4. **凭据永远走环境变量。** Cursor 用 `${env:NAME}`，Hermes 用 `env:` 段显式声明。别把 token 硬编码进配置文件然后提交到仓库。
5. **自动执行不等于安全。** Cursor 文档写得很明白："Auto-review is not a security boundary."删库、发布、转账这类操作，人工确认环节别省。

---

三者的关系其实可以收束成一句很朴素的话：

> **MCP 让 Agent 手更长，Function Calling 让 Agent 手能动，Skills 让 Agent 知道手该往哪儿伸。**

三样都配齐，你才算真正拥有一个"能干活"的 AI 助理，而不只是一个"能聊天"的模型。

---

*本文最后更新于 2026 年 7 月 28 日*
