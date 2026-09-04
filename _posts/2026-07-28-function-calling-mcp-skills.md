---
title: "把 Function Calling、MCP、Skills、Plugin 一次讲清楚"
date: 2026-07-28
categories: [AI]
tags: [Function Calling, MCP, Skills, Plugin, DeepSeek Harness, AI Agent, Cursor, Hermes, 工具调用]
excerpt: "这几个词经常被混着说，但它们处在完全不同的层次上：Function Calling 是模型的能力，MCP 是工具的接口协议，Skills 是给 Agent 的操作手册，而 DeepSeek Harness 带火的 Plugin 是把 Agent 本身拆成可替换零件。本文从零讲起，每个概念配一个能跑的例子，讲清区别、讲清怎么搭配，并给出在 Cursor、Hermes Agent 和 dsh 里的真实配置方法。"
---

> 最近半年，只要聊到 AI Agent，"function calling""MCP""skills"这三个词几乎必然同时出现。很多人的困惑不是"它们是什么"，而是**"它们到底是不是一回事？我该用哪个？"**
>
> 这篇文章写给还没上手的人。看完你应该能回答：这几样东西各自解决什么问题、边界在哪、能不能一起用、在 Cursor、Hermes Agent 和 DeepSeek Harness 里分别怎么配。
>
> **2026 年 9 月更新**：2026 年 8 月 DeepSeek 开源了 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)，打的旗号是"一切皆插件"，于是又多出一个 **Plugin** 的概念。本文新增**第五节**专讲 plugin，并把原来的"三者对比"扩成了四者对比。**如果你只想知道 plugin 和前三者的区别，直接跳到 §5.1 和 §6.3。**

---

## 一、先建立直觉：它们根本不在一个层次上

### 1.1 一句话定义

| 概念 | 一句话说明 | 它是什么"东西" |
| --- | --- | --- |
| **Function Calling** | 让大模型能输出一段"我要调用哪个函数、传什么参数"的结构化请求 | 一种**模型能力** + 一套 API 约定 |
| **MCP**（Model Context Protocol） | 一个开放协议，规定外部工具/数据源如何把自己的能力"自我描述"并暴露给 AI | 一套**通信协议** |
| **Skills** | 一个 Markdown 文件（`SKILL.md`），教 Agent"这类任务应该按什么步骤、什么规范去做" | 一份**操作手册（提示词）** |
| **Plugin**（DeepSeek Harness / Cordis） | 一个代码模块，可以往 Agent 里**注册**能力，也可以**替换掉** Agent 已有的任何一部分——包括模型适配器、审批策略、界面，甚至主循环 | 一个**运行时装配单元** |

前三个是横向的三层，第四个是纵向的：**它是装前三层的容器。** 这是全文的核心，§6.3 会用一张图专门说明。

### 1.2 用"招人"来理解

把 Agent 想象成你新招的一位助理：

- **Function Calling** = 这位助理**会不会填表单**。你给他一沓空白表单（工具定义），他能准确判断该填哪张、每个格子填什么。这是他的**基本能力**，不会填就啥也干不了。
- **MCP** = 公司的**标准接口规范**。以前每个部门（GitHub、数据库、Figma）的表单格式都不一样，助理得为每个部门单独学一遍；有了 MCP，所有部门统一用一种表单格式，助理学一次就能对接所有部门。
- **Skills** = 你写给他的**岗位 SOP 手册**。"发布周报时，先从 Jira 拉数据，再按这个模板排版，标题必须是这个格式……"手册不给他任何新能力，但决定了他做事的**质量和一致性**。
- **Plugin** = 这家公司的**组织架构图，而且交到你手上让你改**。前三样都是"围着这位助理做配置"，plugin 是"重新定义这位助理是谁"：换掉他的大脑（模型）、改掉他的员工手册总纲（系统提示词）、给他派一个合规审查员（工具执行前的硬闸门）、规定他这个岗位能看到哪些工具和哪些 SOP，甚至改掉他"接活—干活—交活"的工作节奏。

前三者的关系是**叠加**的，不是替代；plugin 则是**套在外面的那一层**：

<div class="mermaid">
graph TB
    U["用户：帮我把本周的 Jira 工单整理成周报"]

    subgraph L4["🧩 Plugin 层 —— 这个 Agent 是谁（装配）"]
        P["装什么模型 / 挂哪些 MCP<br/>加载哪些 Skill / 谁来审批"]
    end

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

    U --> P
    P --> S
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
    style P fill:#d4bbff,color:#3d3556
    style S fill:#ffdac1,color:#3d3556
    style B fill:#c7ceea,color:#3d3556
    style M1 fill:#bee3db,color:#3d3556
    style M2 fill:#bee3db,color:#3d3556
    style M3 fill:#bee3db,color:#3d3556
    style R fill:#e2f0cb,color:#3d3556
    style OUT fill:#b5ead7,color:#3d3556
</div>

记住这句话，后面所有内容都是它的展开：

> **Function Calling 决定"模型能不能调工具"，MCP 决定"工具怎么接进来"，Skills 决定"这些工具该怎么用"，Plugin 决定"这个 Agent 由什么组成"。**

> **注**：Plugin 这一层不是所有产品都有。Cursor、Claude Code、Hermes 都只开放了前三层；把"整个产品都是插件"做到底的目前主要是 DeepSeek Harness。所以你可以先按三层理解，第五节再来补第四层。

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

## 五、Plugin：把 Agent 本身拆成可替换的零件

> **本节是 2026 年 9 月新增的。** 前四节写于 2026 年 7 月，当时"三件套"的说法足够用。2026 年 8 月 13 日 DeepSeek 开源了 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（简称 `dsh`），打的旗号是**"一切皆插件"（Everything is a plugin）**，于是又多出一个必须搞清楚的词：**Plugin**。

### 5.1 先说清它和前三者的根本差别

前三者都是在**给 Agent 加东西**：

- Function Calling 加**工具**
- MCP 加**外部系统**
- Skills 加**流程规范**

它们有一个共同的隐含前提：**Agent 本体是厂商写好的，你只能在它留给你的插槽上做扩展。** 模型怎么调用、提示词怎么拼、工具执行前要不要审批、界面长什么样——这些都是黑盒。

Plugin 打破的就是这个前提：

> **Plugin 不是"给 Agent 加东西"，而是"Agent 本身就是由这些东西拼出来的"。**

dsh 的架构文档里有一句话，把这件事说得比任何比喻都清楚：

> "不存在需要打补丁的特权内核：扩展 dsh 的方式是把插件挂载到其他插件旁边。"

模型适配器是插件、工具注册表是插件、会话日志是插件、审批策略是插件、UI 是插件——**连驱动 Agent 转起来的那个主循环（agent loop）本身也是插件**，可以整个换掉。运行中的 `dsh` 就是一棵启动时按序叠出来的插件树，你可以用一条命令把它打印出来：

```bash
dsh --profile web --dump-config
```

**它打印出的任何一个条目，你都能用自己的配置替换掉。**

### 5.2 回到"招人"的比喻

沿用第 1.2 节的类比。前三者是围绕"这位助理"做事：

| 概念 | 对应现实 |
| --- | --- |
| Function Calling | 他**会填表单**（基本能力） |
| MCP | 公司有**统一的表单格式**（对接规范） |
| Skills | 你给他一本**岗位 SOP 手册**（做事规范） |

Plugin 换了个维度——**你不再是"给助理配东西"，而是拿到了这家公司的组织架构图，可以自己改**：

- 把他的**大脑换掉**（换模型适配器：换成本地模型、换成另一家的 API）
- 重写他的**员工手册总纲**（替换系统提示词的组装方式，不是加一份 SOP，是改总纲）
- 给他配一个**合规审查员**（在工具执行前插一道闸门：这条命令不许跑）
- 改掉他的**汇报方式**（换 UI、换会话存储、换日志投影）
- 甚至改掉他**"接活—干活—交活"的工作节奏**（换 agent loop）
- 再规定**哪个岗位能看到哪些工具、哪些 SOP、哪些下属**（agent preset）

一个 dsh 的"写作插件"能同时做到这些事：加一个 `/headline` 命令、注册一个模型可自主调用的起标题工具、换掉写作场景的系统提示词、接入你现有的写作 Skills、挂一个网页检索或外部 MCP、把素材和长期偏好存下来、在界面里加一个写作面板、再限制这个 Agent 只能用哪些 shell 和文件工具。

**换成前三者，这一整套要在三四个不同的地方分别配置，而且有一半根本配不出来。**

### 5.3 Plugin 能碰到的扩展点（这张表最能说明问题）

dsh 里所有能力都挂在一个共享上下文 `ctx` 上，插件通过声明 `inject` 拿到自己依赖的服务。下面这张表来自官方文档的"新行为归属位置"，我挑了最能体现"和前三者不同"的几行：

| 你想做的事 | 机制 | 前三者能做到吗 |
| --- | --- | --- |
| 加一个模型可调用的工具 | 在 `ctx.tools` 上注册，schema 自动进提示词 | ✅ Function Calling / MCP 也能 |
| 接一个 MCP Server | 一个 server 一个插件：发现工具 → `ctx.tools.register()` | ✅ 这本来就是 MCP 干的 |
| 加一条 `/xxx` 用户命令 | 在 `ctx.commands` 上注册，**不需要模型轮次就能派发** | ❌ 做不到 |
| 换一个模型提供方 | 在 `ctx.llm` 上注册适配器 | ❌ 做不到 |
| 拦截工具调用、加审批闸门 | 监听 `tools/pre-execute`，返回类型化的允许/拒绝决策 | ❌ Skill 只能"叮嘱"，拦不住 |
| 限制启动的进程 | 用 `ctx.sandbox` 后端包装 argv | ❌ 做不到 |
| 让某个会话拥有完全不同的能力集合 | 组装一个 agent preset | ⚠️ 只能全局开关工具 |
| 加后台长任务 | 在 `ctx.jobs` 上注册，用 `job_*` 工具收集或停止 | ❌ 做不到 |
| 加持久的会话状态 | 扩展 `SessionEventMap`，从日志渲染和回放 | ❌ 做不到 |
| 换掉界面 / 编辑器集成 | 驱动 `ctx.agents`，从 `session/event` 渲染 | ❌ 做不到 |
| **换掉 agent 主循环本身** | 替换 `ctx.agentLoop` 的提供方 | ❌ 想都别想 |

看这张表的**右边一列**就够了：只有开头两行是前三者的地盘，从第三行往下全是它们碰不到的东西。**这就是 plugin 的增量所在。**

### 5.4 一个能跑的最小插件

概念说完，看代码就很快。一个 dsh 插件本质上就是**一个导出 `apply(ctx)` 的函数**：

```typescript
import type { Context } from '@deepseek-ai/cordis'
import { defineTool } from '@deepseek-ai/dsh-tools'

// 声明依赖：Cordis 会等 ctx.tools 就绪后才执行 apply
export const inject = ['tools']

export function apply(ctx: Context) {
  // 1. 注册一个模型可以自主调用的工具
  ctx.tools.register(defineTool({
    name: 'diag_dtc_lookup',
    description: '根据 DTC 故障码查询含义与处置建议。用户提到故障码、P0xxx、U0xxx 时使用。',
    parameters: {
      type: 'object',
      properties: { code: { type: 'string' } },
      required: ['code'],
      additionalProperties: true,   // object 输出 schema 必须写，否则注册直接失败
    },
    async execute(args) {
      return { code: args.code, meaning: await lookup(args.code) }
    },
  }))

  // 2. 在工具执行前插一道闸门 —— 这是 Skill 做不到的
  ctx.on('tools/pre-execute', (exec, next) => {
    if (exec.name === 'bash' && /rm\s+-rf/.test(exec.args.command)) {
      return { decision: 'deny', reason: '禁止递归删除' }
    }
    return next()   // 瀑布式事件：不调 next() 就把后面的监听器短路了
  })
}
```

三个细节值得记住，因为它们体现了这套设计的性格：

1. **`inject` 决定加载顺序。** 你不 import 具体实现，只声明"我需要 `tools` 这个服务"，框架等它就绪再执行你。所以**加载顺序由依赖决定，而不是文件顺序**。依赖的服务被替换掉时，你的插件会自动卸载，等新提供方上来再重新加载。
2. **所有注册都是可撤销的副作用。** 工具、提示词片段、适配器、事件监听器，全部通过 `ctx.on()` / `ctx.effect()` 注册；插件卸载（热重载、关停）时**自动全部回滚**。这是"一切皆插件"敢让你换掉核心的底气所在。
3. **瀑布式事件必须调 `next()`。** `tools/pre-execute`、`agent/pre-step`、`llm/stream` 都是 waterfall 事件，忘了调 `next()` 就等于把后面所有监听器短路——这是新手最容易踩的坑。

安装方式：

```bash
# 装社区插件 / 本地目录 / GitHub 仓库
dsh plugin --profile web add <包名>
dsh plugin --profile web add ./my-plugin
dsh plugin --profile web add github:yourname/your-repo
```

社区插件都会打 `dsh-plugin` 这个 GitHub topic，awesome 目录里已经有 900+ 个。

### 5.5 "一个能力 = 三个角色"：Seam 这个概念值得单独记

dsh 里有个叫 **seam（能力接缝）** 的设计，理解它就理解了为什么核心能被换掉。**一项可替换能力必须同时设计三个角色**：

| 角色 | 职责 | 例子（Skills 能力族） |
| --- | --- | --- |
| **Service Definition** | 声明接口，挂在 `ctx.xxx` 上 | `dsh-skill` → `ctx.skills` |
| **Service Provider** | 实现这个接口 | `dsh-skill-filesystem`（从磁盘扫 `SKILL.md`） |
| **Consumer** | 消费它，通常是给模型用的工具 | `dsh-tool-skill`（暴露一个 `skill` 工具给模型） |

**换掉提供方就换掉了整个产品的行为**，而消费方一行都不用改。官方举的例子很有说服力：文件系统和进程提供方共享同一个"执行世界"，所以你**把它们指向远程沙箱，Bash、PTY、LSP 就一起搬过去了**，不需要为每个功能单独做一份远程版本。

顺手也把一个高频问题回答了：**dsh 里的 Skill 到底还在不在？**

在，而且格式完全没变——**和 Anthropic 给 Claude Code 定的是同一套契约**：带 YAML frontmatter 的 Markdown。放对目录就会被自动扫到，不需要任何配置：

| 优先级 | 路径 | 什么时候用 |
| --- | --- | --- |
| 200 | `<项目>/.agents/skills` | 技能只属于这个仓库，要随仓库一起走 |
| 500 | `~/.agents/skills` | 技能是你个人的，别的 Agent 框架也要用 |

所以准确的说法是：**"一切皆插件"没有吞掉 skill，只是把它变成了"由插件系统发现、注册、加载的内容"。** 插件解决"能做什么"，skill 解决"怎么做"，两者是互补的。

### 5.6 别的产品有类似的东西吗

有，但都不完整——这也是 plugin 值得单列一节的原因：

| 产品 | 类似机制 | 差在哪 |
| --- | --- | --- |
| Cursor | Extensions（继承自 VS Code）、Hooks | 能改编辑器，**改不了 Agent 内核**：模型怎么调、循环怎么转，都是黑盒 |
| Claude Code | Hooks、MCP、Skills、Subagents | 扩展点是**官方定义好的几个位置**，超出范围就没办法 |
| Hermes Agent | `tools/` 目录 + toolsets，源码可改 | 开源可改，但**改的是源码不是配置**，升级要处理合并冲突 |
| DeepSeek Harness | Plugin（Cordis） | 扩展点就是框架自己，**改配置不改源码**，升级不打补丁 |

一句话概括这个差别：**别人是"在核心外面加东西"，dsh 是"连核心本身都能换"。**

### 5.7 什么时候真的需要写 Plugin

说了这么多好处，也得说清代价。Plugin 的能力上限最高，**维护成本也最高**，而且和框架绑死——你的 dsh 插件搬不到 Cursor，反之亦然（这一点和 MCP 恰好相反）。

**该写 plugin 的场景：**

- 要接入自研模型或内网模型服务（换 `ctx.llm` 适配器）
- 要给 Agent 加**强制性**安全闸门——比如生产环境禁止某类命令。注意这里的关键词是"强制"：Skill 里写"禁止执行"只是叮嘱，模型可以不听；`tools/pre-execute` 返回 deny 是真拦得住
- 要为不同岗位做**能力隔离**（诊断工程师 preset / 代码评审 preset，各自只看到自己该看的工具和 skill）
- 要把 Agent 嵌进自家产品的界面里
- 要改 Agent 的运行节奏（多轮策略、自定义子 Agent 调度、工作流编排）

**不该写 plugin 的场景：**

- **"Agent 老是不按我的要求做事"** → 这是 Skill 的问题，写 Markdown
- **"Agent 查不到我们的数据"** → 这是 MCP 的问题，写 Server（还能顺便给别的 AI 客户端用）
- **"我只想加一个工具"** → MCP 或者直接 function calling 就够了，别为一个工具引入框架依赖

---

## 六、四者的区别：一张表说清

### 6.1 全维度对比

| 对比维度 | Function Calling | MCP | Skills | Plugin |
| --- | --- | --- | --- | --- |
| **层次** | 模型能力层 | 传输/接口层 | 提示词/知识层 | 运行时装配层 |
| **本质是什么** | 模型输出结构化 JSON 的能力 | 一套 client-server 通信协议 | 一个 Markdown 文件 | 一个挂到 `ctx` 上的代码模块 |
| **谁定义** | 应用开发者写 JSON Schema | MCP Server 作者 | 使用者/团队自己写 | 插件作者（框架内/社区） |
| **谁执行** | 你的应用代码 | MCP Server 进程 | 没有"执行"，它只是指令 | Agent 进程内（同一进程，非独立服务） |
| **是否增加新能力** | ✅ 是（把外部函数接进来） | ✅ 是（把外部系统接进来） | ❌ 否（只改变做事方式） | ✅✅ 是，而且能**替换已有能力** |
| **能改 Agent 自身行为吗** | ❌ | ❌ | ⚠️ 只能"建议" | ✅ 能改提示词、审批、循环、UI |
| **跨应用复用** | ❌ 每个应用要重写 | ✅ 一次编写处处可用 | ✅ 目录拷贝即可 | ❌ **绑定框架**，dsh 插件搬不到 Cursor |
| **是否需要写代码** | ✅ 需要 | ✅ 需要（写 server） | ❌ 不需要，写 Markdown | ✅ 需要（TypeScript + 框架 API） |
| **进程边界** | 无（应用内） | ✅ 独立进程/远端，天然隔离 | 无 | 无，**和 Agent 同生共死** |
| **上下文开销** | 每个工具的 schema 常驻 | 同上（MCP 工具最终也变成 function schema） | 平时只有 description，正文按需 | 取决于它注册了什么，可按 preset 隔离 |
| **典型产物** | 一段 `tools=[...]` 定义 | 一个可执行的 server 程序 | 一个 `SKILL.md` 文件 | 一个 npm 包（导出 `apply` + `inject`） |
| **失效表现** | 模型不调用/参数错 | 连不上/工具发现失败 | Agent 不按规范做事 | 插件加载失败、依赖服务缺失、框架升级后 API 不兼容 |
| **心智模型** | 会填表单 | 统一的表单格式 | 岗位 SOP 手册 | 公司的组织架构图 |

两条最容易被忽略的行，是理解 plugin 的关键：

- **跨应用复用**：MCP 和 Skills 都是"写一次、到处能用"，plugin 反过来——它换来的能力上限，代价是**锁定框架**。
- **进程边界**：MCP Server 崩了，Agent 还活着；plugin 写崩了，整个 Agent 一起崩（好消息是 Cordis 的注册都可回滚，卸载插件会自动撤销它的副作用）。

### 6.2 关键洞察：MCP 最终也变成 Function Calling

这一点很多人没想明白，但它是理解四者关系的钥匙：

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

### 6.3 关键洞察二：Plugin 不是第四个并列项，而是装其他三个的容器

这是全文最重要的一句话。前三者是**平行的三层**，plugin 跟它们不平行——**它是那个把三层装起来的盒子**：

<div class="mermaid">
graph TB
    subgraph P["🧩 Plugin：装配层 —— 决定这个 Agent 由什么组成"]
        S["📖 Skills 加载<br/>dsh-skill-filesystem<br/>扫描 SKILL.md 并注册"]
        M["🔌 MCP 接入<br/>一个 server 一个插件<br/>发现工具后注册"]
        B["🧠 工具注册表 ctx.tools<br/>schema 进提示词组装<br/>= Function Calling"]
        X["⚙️ 前三者碰不到的部分<br/>模型适配器 / 审批闸门<br/>沙箱 / UI / agent loop"]
    end

    S --> B
    M --> B
    B --> LLM["模型看到统一的<br/>工具清单 + 指令"]
    X -.约束与承载.-> B

    style S fill:#ffdac1,color:#3d3556
    style B fill:#c7ceea,color:#3d3556
    style M fill:#bee3db,color:#3d3556
    style X fill:#f7c6d9,color:#3d3556
    style LLM fill:#b5ead7,color:#3d3556
</div>

看图里的两个细节：

1. **Skills 是被插件加载的内容。** dsh 里扫描 `SKILL.md`、把它喂给模型的，是 `dsh-skill-filesystem` 和 `dsh-tool-skill` 这两个插件。所以"有了 plugin 还需要 skill 吗"这个问题本身就问错了——skill 是**内容**，plugin 是**加载内容的机制**。
2. **MCP 是插件的一种实现方式。** dsh 官方 cookbook 里写得很直白：**"MCP：一个 server 一个插件——发现工具 → `ctx.tools.register()`"**。MCP 工具进到 Agent 里，走的是和内置工具完全相同的那条路。

所以正确的心智模型是**包含关系**，不是并列关系：

> **Plugin ⊃ { MCP 接入, Skills 加载, 工具注册（→ Function Calling）, 以及前三者都碰不到的部分 }**

那"前三者都碰不到的部分"具体是什么？就是 §5.3 那张表里的东西：模型适配器、提示词组装、审批闸门、沙箱、会话存储、UI、agent loop。**这才是 plugin 真正的增量。**

### 6.4 五个常见误区

| 误区 | 纠正 |
| --- | --- |
| "MCP 比 Function Calling 更先进，应该淘汰后者" | 两者是上下游关系。MCP 工具最终仍然通过 function calling 被模型调用 |
| "Skills 就是把 prompt 存成文件" | 本质上是，但关键在于**按需加载机制**和**跨工具标准化**，这让它可维护、可共享 |
| "有了 Skills 就不用 MCP 了" | Skill 没有执行能力。让 Agent 查数据库，光靠 Markdown 是查不出来的 |
| **"一切皆插件，那 Skill 和 MCP 都要被淘汰了"** | 恰恰相反。dsh 的 skill 用的是**和 Claude Code 完全相同**的 YAML + Markdown 契约，MCP 也是一等接入方式。plugin 淘汰的不是它们，而是"框架给你留几个固定插槽"这种设计 |
| **"Plugin 就是 MCP 的国产版"** | 两者根本不同层。MCP 是**跨产品协议**（进程隔离、可复用）；plugin 是**框架内部的装配单元**（同进程、绑框架）。dsh 里一个 MCP server 就是由一个 plugin 包进来的 |

---

## 七、能不能搭配使用？——不仅能，而且这才是正确姿势

### 7.1 它们本来就是为叠加设计的

四者互不冲突，因为它们各管一段。一个成熟的 Agent 配置通常长这样：

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

在支持 plugin 的框架里，上面这三个框会被再套一层：**是 plugin 决定了这个会话到底能看见哪些 Skill、挂载哪些 MCP、开放哪些内置工具**。换句话说，前三层是"内容"，plugin 是"装配单"。

### 7.2 完整实例：线上故障响应

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

**如果再加一层 plugin 会怎样？** 上面这套配置有个隐患：那三个 MCP、二十来个工具对**所有会话**都可见，做代码评审时也被迫背着 Grafana 的工具描述。用 plugin 可以把这一整套打包成一个 `incident-response` preset——挂载三个 MCP、注册一个 `/incident` 命令、绑定这个 Skill、在 `tools/pre-execute` 上加一道"回滚类命令一律拒绝"的硬闸门。切到这个 preset 才有这些能力，平时完全不占上下文。**Skill 的"禁止自动执行"是一句叮嘱，plugin 的闸门是一道锁。**

### 7.3 什么时候用哪个：决策树

<div class="mermaid">
graph TB
    Q1{Agent 缺的是<br/>能力还是规范?}
    Q1 -->|缺规范| SK[写 Skill<br/>SKILL.md]
    Q1 -->|缺能力| Q2{这个能力<br/>别的工具也想用?}
    Q1 -->|"缺的是产品形态<br/>(想换模型/循环/UI<br/>想加硬闸门)"| Q4{框架支持<br/>plugin 吗?}

    Q2 -->|只有我自己用| FC[直接写 Function<br/>硬编码工具]
    Q2 -->|想跨工具复用| Q3{已经有现成的<br/>MCP Server 吗?}

    Q3 -->|有| USE[直接配置使用]
    Q3 -->|没有| BUILD[自己写一个<br/>MCP Server]

    Q4 -->|"支持 (dsh)"| PL[写 Plugin<br/>装配 / 钩子 / 替换]
    Q4 -->|不支持| GIVE[退回 Skill + MCP<br/>用规范约束，别硬改框架]

    SK -.经常需要.-> Q2
    PL -.内部照样用.-> Q2

    style Q1 fill:#c7ceea,color:#3d3556
    style Q2 fill:#c7ceea,color:#3d3556
    style Q3 fill:#c7ceea,color:#3d3556
    style Q4 fill:#c7ceea,color:#3d3556
    style SK fill:#ffdac1,color:#3d3556
    style FC fill:#b5ead7,color:#3d3556
    style USE fill:#bee3db,color:#3d3556
    style BUILD fill:#bee3db,color:#3d3556
    style PL fill:#d4bbff,color:#3d3556
    style GIVE fill:#e2f0cb,color:#3d3556
</div>

一条经验法则：**先用 Skill 试。** 很多你以为需要开发工具的场景，其实只是没把流程讲清楚。Skill 的成本是写一个 Markdown 文件，MCP Server 的成本是写、测、部署、维护一个程序，Plugin 的成本还要再加上"跟着框架版本升级"。

按成本从低到高排队，就是最实用的选型顺序：

> **Skill（写字） → MCP（写程序） → Function（改应用代码） → Plugin（改 Agent 本体）**

往下走一格，能力上限抬一档，维护负担也抬一档。**能在上一格解决的，别下沉。**

---

## 八、在 Cursor 里怎么用

> 以下配置对应 Cursor 3.6 前后的版本（2026 年年中）。Cursor 的审批机制在 2026 年上半年改过两次，看到旧教程里的 "YOLO mode" 请自动翻译成现在的 Run Modes。

### 8.1 Function Calling：内置工具 + 审批模式

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

### 8.2 MCP：`.cursor/mcp.json`

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

### 8.3 Skills：`SKILL.md` 放对地方就行

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

### 8.4 Skills 和 Rules 是两个东西

这是 Cursor 用户最容易混的一对。官方帮助页的区分是：

- **Rules**（`.cursor/rules/*.mdc`、`AGENTS.md`）：**短的编码准则和约束**，作为上下文加进每次（或匹配的）对话。
- **Skills**（`SKILL.md`）：**多步骤的工作流和规程**，按需用 `/skill-name` 或 `@skill-name` 调起。

一个提醒：`.cursor/rules` 目录里的 `.md` 文件**会被忽略**，项目规则必须是 `.mdc`——因为 `.md` 没地方写 `description`、`globs`、`alwaysApply` 这些 frontmatter。

如果你以前写了一堆 rules，Cursor 内置了 `/migrate-to-skills` 帮你迁移。

### 8.5 三者合体的样子（Cursor 里没有 Plugin 这一层）

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

## 九、在 Hermes Agent 里怎么用

[Hermes Agent](https://github.com/NousResearch/hermes-agent) 是 Nous Research 开源的自我改进型个人 Agent（我之前写过一篇[基于源码的解析]({{ '/2026/04/21/hermes-framework/' | relative_url }})）。它对这三样东西的支持很完整，但配置路径和 Cursor 完全不同。

### 9.1 Function Calling：`tools/` 目录 + toolsets

Hermes 的工具都在仓库的 `tools/` 目录下，一个工具一个文件，由 `tools/registry.py` 统一注册。核心的几个：

| 文件 | 提供的能力 |
| --- | --- |
| `terminal_tool.py` | 终端执行，支持 local / docker / ssh / daytona / singularity / modal 六种后端 |
| `file_tools.py` | read / write / search / patch |
| `web_tools.py` | 搜索与抓取 |
| `browser_tool.py` | 浏览器自动化 |
| `delegate_tool.py` | 派发 subagent，可多路并行 |
| `mcp_tool.py` | MCP 客户端 |
| `approval.py` | 危险命令探测（`rm -rf`、把远程脚本直接管道给 shell 执行之类），配合人工确认 |

控制哪些工具启用：

```bash
hermes tools           # 交互式启用/禁用内置工具
```

有一点要特别注意：**用自己部署的模型时，一定要选带 function call / tools 支持的版本**。比如 Qwen 系列要用 Instruct 版，否则 `tools/` 里的 schema 派发会整个失效，Agent 会退化成一个只会聊天的 chatbot。

### 9.2 MCP：`~/.hermes/config.yaml` 里的 `mcp_servers`

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

### 9.3 Skills：`~/.hermes/skills/`

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

### 9.4 Cursor vs Hermes vs dsh：配置速查

| 对比项 | Cursor | Hermes Agent | DeepSeek Harness |
| --- | --- | --- | --- |
| **MCP 配置文件** | `.cursor/mcp.json` / `~/.cursor/mcp.json` | `~/.hermes/config.yaml` | profile 的 `cordis.patch.yml`（一个 server = 一个插件条目） |
| **MCP 配置格式** | JSON，`mcpServers` 键 | YAML，`mcp_servers` 键 | YAML，Cordis 插件树条目 |
| **MCP 工具命名** | 原名 | `mcp_{server}_{tool}` 前缀 | 由该 MCP 插件决定，最终走 `ctx.tools.register()` |
| **Skills 目录** | `.cursor/skills/`、`.agents/skills/`（含全局版） | `~/.hermes/skills/`（可再分类目） | `<项目>/.agents/skills`、`~/.agents/skills` |
| **Skill 额外字段** | `paths`、`disable-model-invocation` | `aliases`、`categories`、`version` | 与 Claude Code 同一套契约，无私有字段 |
| **工具开关** | Customize → MCPs / `permissions.json` | `hermes tools` / `hermes skills` | agent preset + `ctx.tools.restrict()` |
| **审批机制** | Run Modes（Auto-review / Allowlist / Run Everything） | `approvals.mode` + `tools/approval.py` 危险命令探测 | `tools/pre-execute` 瀑布式事件 + sandbox 插件，**策略本身可替换** |
| **第三方扩展安装** | Customize → Rules → Remote Rule (Github) | `/skills` 或 `hermes skills install` | `dsh plugin --profile <p> add <包名｜路径｜github:user/repo>` |
| **能换掉模型适配器 / 主循环吗** | ❌ | ❌ | ✅ 这正是 plugin 的设计目标 |

看最后一行就明白 plugin 这一层为什么值得单独讲：**前两个产品的扩展点是"框架留给你的插槽"，dsh 的扩展点是"框架自己"。**

---

## 十、GitHub 上值得关注的仓库

下面这些仓库我都通过 GitHub API 核对过，星标数为 **2026 年 7 月**的快照。

### 10.1 MCP 官方与基础设施

| 仓库 | Stars | 说明 |
| --- | --- | --- |
| [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) | 89.0k | **官方参考实现集合**，filesystem、git、fetch、memory 等。想学 MCP 从这里读源码 |
| [modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk) | 23.7k | 官方 Python SDK，`FastMCP` 就在里面 |
| [modelcontextprotocol/typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk) | 13.0k | 官方 TypeScript SDK |
| [modelcontextprotocol/inspector](https://github.com/modelcontextprotocol/inspector) | 10.5k | **可视化调试工具**，写 server 时的必备品，能直接看到工具列表和调用结果 |
| [PrefectHQ/fastmcp](https://github.com/PrefectHQ/fastmcp) | 26.9k | 更 Pythonic 的 MCP 框架，装饰器风格 |
| [mcp-use/mcp-use](https://github.com/mcp-use/mcp-use) | 10.4k | 全栈 MCP 框架，同时做 MCP App 和 MCP Server |

### 10.2 现成可用的 MCP Server

| 仓库 | Stars | 说明 |
| --- | --- | --- |
| [punkpeye/awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) | 91.5k | **MCP server 大全**，找工具先来这里 |
| [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) | 35.6k | 微软官方，浏览器自动化。让 Agent 真正能点网页 |
| [github/github-mcp-server](https://github.com/github/github-mcp-server) | 31.8k | GitHub 官方，issue / PR / 仓库操作 |
| [googleapis/mcp-toolbox](https://github.com/googleapis/mcp-toolbox) | 16.0k | 数据库 MCP，Google 出品 |
| [GLips/Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP) | 15.5k | 把 Figma 设计稿信息喂给 Cursor，设计转代码常用 |
| [awslabs/mcp](https://github.com/awslabs/mcp) | 9.5k | AWS 官方的一套 MCP Server |
| [hangwin/mcp-chrome](https://github.com/hangwin/mcp-chrome) | 12.2k | 基于 Chrome 扩展，能操作你**现有的**浏览器（带登录态） |

### 10.3 Skills 仓库

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

### 10.4 Plugin 生态（DeepSeek Harness）

这一类是 2026 年下半年才出现的，星标增长很快，我就不标快照数字了（免得半个月就过期）：

| 仓库 | 说明 |
| --- | --- |
| [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) | **主仓库**。想搞懂 plugin 架构，直接读 `docs/subsystems/` 和 `docs/cookbook/extension-cookbook.md`，是全网写得最清楚的一手材料 |
| `packages/skill/*`（同仓库） | `dsh-skill` / `dsh-skill-filesystem` / `dsh-tool-skill` 四件套，**"一个能力 = 定义 + 提供方 + 消费方"的最佳教学案例** |
| `docs/develop/cordis-tutorial`（同仓库） | Cordis 框架的动手教程，七章从"函数插件"写到"接进真实 harness 服务"，两小时能跑完 |
| GitHub topic：[`dsh-plugin`](https://github.com/topics/dsh-plugin) | 社区插件都打这个 topic，awesome 目录里已有 900+ 个公开插件。找现成的先来这里 |

> 一个提醒：dsh 目前还是**开发者预览版**，核心仓库暂不接受外部 PR，官方把贡献路径指向生态（发插件、写教程、报 issue）。核心插件和基础 API 仍在迭代，写插件要做好跟版本的准备。

### 10.5 同时用到 MCP + Skills 的项目（最值得读）

想看"多层怎么配合"，读这几个仓库最直观：

| 仓库 | Stars | 为什么值得看 |
| --- | --- | --- |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | 221.5k | 内置 MCP 客户端（`tools/mcp_tool.py`）+ 25 个类目的 skill 体系 + 自我改进闭环，本文第九节的配置全部来自它 |
| [danny-avila/LibreChat](https://github.com/danny-avila/LibreChat) | 41.4k | 开源 ChatGPT 替代品，Agents + MCP + Skills 全都有，前后端完整 |
| [zylon-ai/private-gpt](https://github.com/zylon-ai/private-gpt) | 57.4k | 私有化部署的完整 API 层：RAG、skills、tools、MCP 一应俱全 |
| [sickn33/agentic-awesome-skills](https://github.com/sickn33/agentic-awesome-skills) | 44.0k | 1987+ skill 的目录服务，**本身就带一个 local MCP** ——用 MCP 来分发 skill，思路很妙 |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | 46.2k | 中文社区的老牌项目（原 chatgpt-on-wechat），工具 + skill + 记忆的完整实现 |
| [activepieces/activepieces](https://github.com/activepieces/activepieces) | 23.4k | 约 400 个 MCP Server 的自动化平台，看它怎么做工作流编排 |

> **上手建议**：先 clone [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) 挑最简单的 `fetch` 读一遍，再 clone [anthropics/skills](https://github.com/anthropics/skills) 挑一个 skill 读一遍。两个仓库加起来两小时，比看十篇教程管用。

---

## 十一、总结

### 11.1 四句话记住区别

- **Function Calling** 是**模型的能力**——它让 LLM 能输出"我要调这个函数、传这些参数"。没有它，一切免谈。
- **MCP** 是**工具的接口**——它让一个工具写一次，所有 AI 客户端都能用。它最终还是喂给 function calling 的。
- **Skills** 是**Agent 的手册**——它不给新能力，但决定 Agent 做事专不专业、稳不稳定。
- **Plugin** 是**Agent 的零件**——它不只给工具，而是能替换模型适配器、提示词、审批策略、UI，甚至主循环本身。

### 11.2 上手路线

<div class="mermaid">
graph LR
    S1["1. 用现成的<br/>Cursor / Hermes / dsh<br/>体验内置工具"] --> S2["2. 配一个 MCP<br/>从 filesystem<br/>或 github 开始"]
    S2 --> S3["3. 写第一个 Skill<br/>把你最常重复的<br/>流程写成 SKILL.md"]
    S3 --> S4["4. 写自己的<br/>MCP Server<br/>接内部系统"]
    S4 --> S5["5. 组合<br/>Skill 编排<br/>多个 MCP 工具"]
    S5 --> S6["6. 写 Plugin<br/>改造 Agent 本身<br/>钩子 / 策略 / 循环"]

    style S1 fill:#b5ead7,color:#3d3556
    style S2 fill:#bee3db,color:#3d3556
    style S3 fill:#ffdac1,color:#3d3556
    style S4 fill:#c7ceea,color:#3d3556
    style S5 fill:#e2f0cb,color:#3d3556
    style S6 fill:#d4bbff,color:#3d3556
</div>

**第 6 步是 2026 年新增的台阶**，也是门槛最高的一步：前五步都在"给 Agent 加东西"，第六步开始"改 Agent 自己"。没有明确需求前不要急着上。

### 11.3 几条实践经验

1. **先写 Skill，再造工具。** 很多"Agent 做不好"的问题，本质是你没把要求说清楚，而不是缺工具。写 Markdown 的成本远低于写、测、部署、维护一个 MCP Server。
2. **工具不是越多越好。** MCP Server 接得越多，塞进 prompt 的工具描述就越长，模型选错的概率和 token 成本一起涨。Cursor 文档专门提到关掉不用的 server 可以"减少工具杂讯"，这是真话。
3. **`description` 是最值钱的一行。** 无论是 function 的 description、MCP 工具的 docstring，还是 SKILL.md 的 description——它们都是模型判断"要不要用"的唯一依据。这一行值得反复打磨。
4. **凭据永远走环境变量。** Cursor 用 `${env:NAME}`，Hermes 用 `env:` 段显式声明。别把 token 硬编码进配置文件然后提交到仓库。
5. **自动执行不等于安全。** Cursor 文档写得很明白："Auto-review is not a security boundary."删库、发布、转账这类操作，人工确认环节别省。
6. **别用 Plugin 解决 Skill 能解决的问题。** Plugin 的能力上限最高，维护成本也最高——它跟着框架版本走，框架改了你就得改。凡是"只是流程没说清"的问题，写 Markdown 就够了。

---

四者的关系其实可以收束成一句很朴素的话：

> **MCP 让 Agent 手更长，Function Calling 让 Agent 手能动，Skills 让 Agent 知道手该往哪儿伸，Plugin 让你能换掉这只手、这条胳膊，乃至这个人。**

四样都配齐，你才算真正拥有一个"能干活"的 AI 助理，而不只是一个"能聊天"的模型。

---

*本文最后更新于 2026 年 9 月 3 日（新增 Plugin 一节与四者对比）*
