---
title: "具身智能入门：发展史、系统组成、软件栈与名词表"
date: 2026-07-29
categories: [人工智能]
tags: [具身智能, 机器人, VLA, ROS2, 强化学习, 仿真, 世界模型, Embodied AI]
excerpt: "从图灵 1950 年留下的那道选择题讲起，梳理具身智能七十年的技术路线之争；拆解一套具身智能系统由哪些层组成、每层解决什么问题；系统介绍软件工程师真正会用到的技术栈（ROS 2 / 仿真器 / VLA / 控制）；最后附一份 60+ 条的名词速查表。"
---

> 这是本站 AI 系列的第四篇。前面三篇——[《AI Agent》]({{ '/2026/04/15/ai-agent/' | relative_url }})、[《知识库》]({{ '/2026/04/16/knowledge-base/' | relative_url }})、[《Function Calling、MCP、Skills》]({{ '/2026/07/28/function-calling-mcp-skills/' | relative_url }})——讲的都是**活在屏幕里**的 AI。
>
> 这一篇讲的是**走进物理世界**的 AI。
>
> 说明一下：这个领域最近两年变化极快，网上大量教程的信息已经过期（比如 Isaac Gym 早就归档了、ROS 1 已经 EOL 了、RealSense 并没有"停产"）。本文所有版本号、日期、论文和数据都做了核实并附了出处，无法核实的地方我会明确标注。

---

## 一、先建立直觉

### 1.1 一句话定义

**具身智能（Embodied AI）**：智能体拥有物理身体，通过与真实环境的持续交互来感知、学习和行动。

关键词是**身体**和**交互**。GPT 能写代码，但它拧不开一个瓶盖。让一个 AI 拧开瓶盖，需要的不是更强的语言能力，而是眼睛、手、力反馈，以及在失败几百次之后学会"这个瓶盖有点紧，得先按住再转"的能力。

### 1.2 为什么它这么难：莫拉维克悖论

理解具身智能，绕不开 **Moravec's Paradox（莫拉维克悖论）**。汉斯·莫拉维克在 1988 年的《Mind Children》里这样表述：

> *"it is comparatively easy to make computers exhibit adult level performance on intelligence tests or playing checkers, and difficult or impossible to give them the skills of a one-year-old when it comes to perception and mobility."*
>
> （让计算机在智力测验或下棋上达到成年人水平相对容易，而让它具备一岁小孩的感知和行动能力却极其困难，甚至不可能。）

紧接着那段解释更值得读：

> *"Encoded in the large, highly evolved sensory and motor portions of the human brain is a billion years of experience about the nature of the world and how to survive in it. The deliberate process we call reasoning is, I believe, the thinnest veneer of human thought..."*
>
> （人脑中庞大而高度进化的感知与运动区域，编码了十亿年关于世界本质和如何生存的经验。我们称之为"推理"的那个刻意过程，我认为只是人类思维最薄的一层表皮。）

这就是为什么 2026 年的 AI 能通过律师资格考试，却叠不好一件衣服。**推理是十万年前的新技能，抓取是十亿年前的老本事。**

顺带纠正一个常见误传：这个观察在 1980 年代由 Moravec、Rodney Brooks 和 Marvin Minsky 各自独立提出，只是莫拉维克表述得最精炼，不能只归功于他一个人。

### 1.3 和"软件 Agent"的本质区别

如果你写过 AI Agent，这张表能帮你快速建立坐标系：

| 维度 | 软件 Agent（如 Cursor） | 具身智能 |
| --- | --- | --- |
| **动作空间** | 离散、可枚举（调用哪个工具） | 连续、高维（几十个关节的力矩） |
| **反馈延迟** | 秒级，可等待 | 毫秒级，等不起 |
| **错误代价** | 改回来就行，可以 undo | **物理不可逆**——打碎的杯子粘不回去 |
| **数据来源** | 互联网有海量文本 | **真机数据要一条一条采**，没有互联网可爬 |
| **状态可观测** | 文件内容完全可读 | 部分可观测，传感器有噪声 |
| **仿真** | 不需要 | **必需**，但仿真和现实有差距 |
| **实时性** | 无硬性要求 | 控制回路必须硬实时 |

其中**第三、第四条是具身智能真正的门槛**。语言模型可以在互联网上"白嫖"人类几十年积累的文本，而机器人数据必须有人真的去操作机器人采集——这个差异决定了这个领域的一切技术选择，后面会反复看到。

---

## 二、发展史：七十年的路线之争

### 2.1 时间线总览

<div class="mermaid">
graph TB
    A["1950<br/>图灵提出两条路<br/>并说都该试试"] --> B["1966-1980<br/>符号主义时代<br/>Shakey / STRIPS<br/>❌ 撞上框架问题"]
    B --> C["1986-1991<br/>Brooks 的反叛<br/>行为主义 / 物理接地<br/>✅ 昆虫级能力"]
    C --> D["2004-2015<br/>深度学习解决感知<br/>DARPA 挑战赛<br/>⚠️ 控制还靠手写"]
    D --> E["2015-2021<br/>让控制器自己学<br/>端到端 / 域随机化<br/>⚠️ 卡在数据量"]
    E --> F["2022-2026<br/>基础模型时代<br/>VLA / 跨本体数据<br/>🔥 当前主战场"]
    F --> G["2024-2026<br/>世界模型分叉<br/>Genie / V-JEPA 2"]

    style A fill:#c7ceea,color:#3d3556
    style B fill:#ffd6e0,color:#3d3556
    style C fill:#b5ead7,color:#3d3556
    style D fill:#ffdac1,color:#3d3556
    style E fill:#bee3db,color:#3d3556
    style F fill:#e2f0cb,color:#3d3556
    style G fill:#c7ceea,color:#3d3556
</div>

### 2.2 1950：图灵留下的选择题

一切的起点在图灵 1950 年那篇《Computing Machinery and Intelligence》的最后一节。他提出了两条通往机器智能的路：

> *"Many people think that a very abstract activity, like the playing of chess, would be best. It can also be maintained that it is best to provide the machine with the best sense organs that money can buy, and then teach it to understand and speak English... Again I do not know what the right answer is, but I think both approaches should be tried."*
>
> （很多人认为应该从下棋这样非常抽象的活动开始。但也可以主张，最好是给机器配上钱能买到的最好的感觉器官，然后教它理解和说英语……我不知道哪个答案是对的，但我认为两条路都该试试。）

**抽象符号 vs 感官身体——AI 诞生之初的这道分岔，直接定义了后面七十年。** 有意思的是，第一条路先跑赢了，而如今是它反过来让第二条路变得可行。

### 2.3 1966-1980：符号主义认真试过，撞墙了

**Shakey（SRI，1966-1972）** 是第一台能感知并推理周遭环境的移动机器人。它在放着积木和斜坡的房间里做路径规划和物体搬运。它顺带产出了三样至今还在用的东西：**A\* 搜索算法**、**Hough 变换**、**STRIPS 规划器**。1970 年《生活》杂志称它为"第一个电子人"。

**STRIPS（Fikes & Nilsson, 1971）** 定义了经典的 **sense-plan-act（感知-规划-行动）** 范式：规划器在一个符号化的世界模型里搜索，找到能达成目标的操作序列，然后交给身体执行。

问题出在哪？**框架问题（Frame Problem）**，McCarthy 和 Hayes 在 1969 年就指出了：如果有 n 个动作和 m 个状态量，你可能需要写下 m×n 条规则来说明每个动作**不会**改变哪些东西。现实世界的常识多到无法穷举。

具体有多慢？看**斯坦福小车（Stanford Cart，1979）**，操作者正是莫拉维克本人，数据来自他自己的论文：

> 小车每 10 到 15 分钟移动一米，"一顿一顿地"走：挪一米、停下、拍照、想很久、重新规划、再挪一点。有效速度 **3-5 米/小时**。走完一段 20 米的路程需要**大约五个小时**。

**五个小时穿过一个房间**——这就是莫拉维克悖论在被命名之前的样子。

### 2.4 1986-1991：Brooks 说，问题出在"模型"本身

MIT 的 **Rodney Brooks**（后来创办了 iRobot）发起了一场反叛。他的三篇论文是这个领域的必读：

- 《A Robust Layered Control System for a Mobile Robot》（1986）——提出**包容架构（Subsumption Architecture）**
- 《Elephants Don't Play Chess》（1990）
- 《Intelligence Without Representation》（1991，实际投稿于 1987）

他的核心论点，原文摘自《Elephants Don't Play Chess》：

> *"Nouvelle AI is based on the physical grounding hypothesis. This hypothesis states that to build a system that is intelligent it is necessary to have its representations grounded in the physical world... The key observation is that **the world is its own best model**. It is always exactly up to date. It always contains every detail there is to be known. The trick is to sense it appropriately and often enough."*
>
> （新式 AI 建立在**物理接地假说**之上：要造出智能系统，其表征必须扎根于物理世界……关键的洞察是，**世界就是它自己最好的模型**。它永远是最新的，永远包含所有细节。诀窍在于以恰当的方式、足够频繁地去感知它。）

标题那句话的出处也在这篇：

> *"We do not usually complain that a medical expert system... cannot climb real mountains... Likewise it is unfair to claim that an elephant has no intelligence worth studying just because it does not play chess."*
>
> （我们通常不会抱怨医疗专家系统不会爬山……同样，仅仅因为大象不下棋就说它没有值得研究的智能，这不公平。）

Brooks 的机器人 **Genghis** 是个 1 公斤重的六足步行机器人，12 个马达、12 个力传感器，但**没有任何中央的世界模型，甚至连每条腿的状态都没有集中存储**。它照样能爬过崎岖地形。

**怎么评价这场争论？** 公道地说，Brooks 的**诊断**是对的（符号模型是负担），但**药方**只对了一半。哲学家 Dreyfus 后来的批评很到位：Brooks 的机器人"**绕过了而非解决了**框架问题"，因为它们只对固定的、可孤立的环境特征做出反应，处理不了上下文和意义的变化。昆虫级的智能确实不需要表征，但你没法用包容架构造出会做饭的机器人。

### 2.5 2004-2015：感知被解决了，控制还是手写的

**DARPA 三场比赛**是这一时期的最佳注脚：

| 比赛 | 时间 | 结果 |
| --- | --- | --- |
| Grand Challenge | 2004-03-13 | **无人完赛**。最好成绩是 CMU 在第 7.4 英里冲出赛道 |
| Grand Challenge | 2005-10-08 | 23 队参赛 5 队完赛，斯坦福 Stanley（Sebastian Thrun 团队）以 6 小时 53 分夺冠 |
| Robotics Challenge 决赛 | 2015-06-05/06 | 24 队参赛，**只有 3 队完成全部 8 项任务，3 队一项都没完成** |

Stanley 的意义不在于赢，而在于它**大量使用了机器学习和概率推理**——这一年之后，概率机器人学（Thrun、Burgard、Fox 的经典教材 2005 年出版）成了标配。

2012 年 **AlexNet** 在 ImageNet 上把 top-5 错误率从 26.2% 打到 **15.3%**，手工设计视觉特征的时代一夜结束。机器人的"眼睛"问题基本被解决了。

但 2015 年的 DRC 决赛暴露了真相：**机器人的"小脑"还是手写的**。那些著名的机器人摔倒集锦是真实的——IHMC 的 Running Man 走完楼梯、举起双臂庆祝、跳了两下，然后绊倒瘫倒在地。冠军 KAIST 的 DRC-HUBO 用时 44 分 28 秒，赢的关键是它能在双足行走和膝盖轮子滑行之间切换，**全程没摔**。团队负责人 Jun-Ho Oh 赛后那句话很经典：

> *"To be a disaster robot, [a robot] must be free from disaster himself."*
>
> （要当救灾机器人，它自己首先不能变成灾难。）

### 2.6 2015-2021：让控制器自己学出来

既然手写控制器不行，那就学。这一时期的关键工作：

**端到端视觉运动策略（Levine, Finn, Darrell, Abbeel, 2015）** —— arXiv:1504.00702。第一次把感知和控制**联合端到端训练**，神经网络直接从原始像素输出电机力矩，在 PR2 上完成拧瓶盖之类的任务。

**Google 的"机械臂农场"（2016）** —— arXiv:1603.02199。同时用 6 到 14 台机械臂，**两个月采集了超过 80 万次抓取尝试**，全程无人标注，唯一的人工干预是往箱子里放回物体。2018 年的 **QT-Opt** 更进一步，**58 万次真机抓取**，对未见过物体达到 **96% 抓取成功率**，还自发学会了"戳一下物体换个角度再抓"这种没人教过的行为。

**域随机化（Domain Randomization，Tobin et al. 2017）** —— 这是解决 sim-to-real 的关键思路，反直觉但很漂亮：**与其把仿真做得更真实，不如把仿真的物理和视觉参数随机到离谱，让现实世界看起来只是训练分布里的又一个样本。**

**OpenAI Dactyl（2018-2019）** 用这招让 Shadow 灵巧手**完全在仿真中训练**、零真机数据地学会转魔方。2019 年的升级版提出了**自动域随机化（ADR）**，让难度自动递增。（提醒：解魔方的算法是传统的 Kociemba，学出来的是手内操作，这点常被夸大。）

**ANYmal（ETH Zurich，Hwangbo et al., Science Robotics 2019）** —— 这篇让强化学习成为足式运动的默认方案。关键不只是 RL，而是他们学了一个**执行器模型**来补上解析模型最不擅长的那部分（串联弹性执行器）。

到这个阶段，路线已经清晰：**能学就别写**。但新瓶颈出现了——**数据从哪来？**

### 2.7 2022-2026：基础模型时代（当前主战场）

答案是：**不要自己采所有数据，去互联网上"借"世界知识。**

<div class="mermaid">
graph LR
    A["SayCan 2022<br/>LLM 提建议<br/>价值函数判可行"] --> B["RT-1 2022<br/>Transformer<br/>直接输出动作"]
    B --> C["RT-2 2023<br/>把动作当文本 token<br/>VLA 范式确立"]
    C --> D["Open X-Embodiment<br/>2023<br/>22 种本体数据汇总"]
    D --> E["OpenVLA 2024<br/>7B 开源<br/>打败 55B 闭源"]
    D --> F["π0 2024 → π0.7 2026<br/>流匹配动作专家"]
    D --> G["GR00T N1 2025<br/>→ N1.7"]
    D --> H["Helix 2025<br/>→ Helix 02 2026"]

    style A fill:#c7ceea,color:#3d3556
    style B fill:#bee3db,color:#3d3556
    style C fill:#b5ead7,color:#3d3556
    style D fill:#ffdac1,color:#3d3556
    style E fill:#e2f0cb,color:#3d3556
    style F fill:#e2f0cb,color:#3d3556
    style G fill:#e2f0cb,color:#3d3556
    style H fill:#e2f0cb,color:#3d3556
</div>

**第一步：让大模型当大脑。** **SayCan**（Google，2022）的设计很巧妙：LLM 提议"该做什么"，学出来的价值函数判断"现在能做什么"，两者相乘选下一个技能。用 PaLM 后，选对技能序列的比例 **84%**，实际执行成功率 **74%**。

**第二步：把动作变成 token。** **RT-2**（Google DeepMind，2023）把机器人动作编码成文本 token，用网页数据和机器人数据**共同微调**一个视觉语言模型。这篇确立了 **VLA（Vision-Language-Action，视觉-语言-动作模型）** 这个品类。

**第三步：把全世界的机器人数据汇到一起。** **Open X-Embodiment**（2023，多机构合作）把各实验室的数据统一格式汇总（常引用的数字是 22 种本体），证明了**跨本体正迁移**——用别的机器人的数据能让你的机器人变强。这个数据集成了后续几乎所有开源 VLA 的基础。

**同期还有两个必须知道的模仿学习架构**，它们不是 VLA，但是 VLA 的零件：

- **Diffusion Policy**（Chi et al., 2023，哥伦比亚/TRI/MIT/斯坦福）：把策略建模成对**动作序列**的条件去噪扩散过程。它解决的是行为克隆的老毛病——当人类演示有多种合理做法时，回归模型会把它们平均成一个错误的中间值。
- **ACT + ALOHA**（Zhao, Kumar, Levine, Finn，2023）：**ALOHA** 是一套**不到 2 万美元**的开源双臂遥操作硬件；**ACT（Action Chunking Transformer）** 一次预测**一整块**未来动作而不是单步。效果惊人：开调料杯盖、插电池、穿扎带这类精细任务，**仅用约 10 分钟的演示数据就达到 80-90% 成功率**。原理是分块缩短了有效决策长度，从而抑制误差累积。

**动作分块（action chunking）这个概念现在是标配，值得记住。**

**当前的主要玩家（截至 2026 年 7 月）：**

| 团队 | 最新公开模型 | 时间 | 特点 |
| --- | --- | --- | --- |
| **Physical Intelligence** | π0.7 | 2026-04-16 | π0 系列，VLM + 流匹配"动作专家"，主打叠衣服等长程任务 |
| **Figure** | Helix 02 | 2026-01-27 | 三层 S0/S1/S2 架构，全身控制 |
| **NVIDIA** | GR00T N1.7 | 2026 年 4 月 | Apache 2.0 开源可商用，人形机器人基础模型 |
| **Google DeepMind** | Gemini Robotics 1.5 + ER 1.6 | ER 1.6 为 2026-04-14 | ER 版本是"能思考的具身推理模型"，可调 Google 搜索 |
| **开源社区** | OpenVLA | 2024-06 | **7B 参数**，用 97 万条真机轨迹训练，在 29 个任务上比闭源的 **RT-2-X（55B）绝对成功率高 16.5%，参数少 7 倍**，MIT 协议 |

Physical Intelligence 的 π 系列演进值得单独看，它清楚展示了这两年的技术走向（日期来自其官方博客）：

| 版本 | 时间 | 关键改进 |
| --- | --- | --- |
| π0 | 2024-10-31 | VLM + 独立"动作专家"，用**流匹配**输出连续动作块，最高 50 Hz |
| π0.5 | 2025-04-22 | 分层：先预测高层子任务再出低层动作，能清理一个全新的厨房 |
| π\*0.6 | 2025-11-17 | **RECAP**：离线 RL 预训练 + 真机自主练习 + 专家接管纠错。官方称在最难的任务上**吞吐量翻倍以上、失败率约减半** |
| π0.7 | 2026-04-16 | 官方称"泛化能力的阶跃式提升"，强调可操控性 |

**注意 π\*0.6 那一步的意义：从"只会模仿人类演示"走向"能从自己的失败中改进"。** 这是模仿学习之外，强化学习重新回到舞台中央的信号。

再看 **Figure Helix** 的架构，因为它的公开数字最完整，是理解现代具身智能系统分层的最佳案例：

| 层 | 参数量 | 频率 | 职责 |
| --- | --- | --- | --- |
| **System 2** | 7B（开源 VLM） | 7-9 Hz | 理解场景和语言指令，"想清楚要干嘛" |
| **System 1** | 80M | 200 Hz | 把意图翻译成连续动作，含手指 |
| **System 0**（Helix 02 新增） | 10M | **1 kHz** | 全身底层控制，管平衡和物理执行 |

几个细节很有说服力：Helix 只用了约 **500 小时**遥操作数据（不到之前 VLA 数据集的 5%）；Helix 02 的 System 0 用**超过 1000 小时的人类动作重定向数据**、在**超过 20 万个并行仿真环境**里训练，**替换掉了 109,504 行手写 C++ 代码**；它的旗舰演示是**连续 4 分钟、61 个动作、全自主完成整个厨房的洗碗机装卸**，手忙不过来时会用胯部关抽屉、用脚挑开洗碗机门。

### 2.8 2024-2026：世界模型这条岔路

在 VLA 之外，还有一条正在快速发展的路线：**世界模型（World Model）**。

DeepMind 的定义最清晰：世界模型是"能利用对世界的理解来模拟世界的 AI 系统，使智能体能预测环境将如何演变、以及自己的动作会带来什么影响"。

它在机器人里有**两种完全不同的用法**，初学者最容易混淆：

<div class="mermaid">
graph TB
    W["世界模型"] --> A["用法一：造数据<br/>生成可交互的训练环境"]
    W --> B["用法二：当大脑里的物理引擎<br/>运行时在脑内推演再决策"]

    A --> A1["Genie 3 (2025-08)<br/>720p / 24fps 实时<br/>一致性维持数分钟"]
    A --> A2["NVIDIA Cosmos (2025-01)<br/>2000 万小时数据训练<br/>面向工业合成数据"]

    B --> B1["V-JEPA 2 (2025-06, Meta)<br/>被动视频学动力学<br/>运行时用 MPC 规划"]

    style W fill:#c7ceea,color:#3d3556
    style A fill:#bee3db,color:#3d3556
    style B fill:#ffdac1,color:#3d3556
    style A1 fill:#e2f0cb,color:#3d3556
    style A2 fill:#e2f0cb,color:#3d3556
    style B1 fill:#b5ead7,color:#3d3556
</div>

**用法二更激进，也更有意思。** Meta 的 **V-JEPA 2**（2025 年 6 月，LeCun 团队）是 LeCun"智能体应该通过观察来学习"主张的具体实现：先用**超过 100 万小时的互联网视频**做无动作标签的预训练，然后用 **DROID 数据集里不到 62 小时的无标注机器人视频**后训练出 V-JEPA 2-AC，就能在**两个不同实验室的 Franka 机械臂上零样本**完成抓取和取放——**没有在那些环境里采过任何数据，没有任务专用训练，也没有奖励函数**，靠的是在潜空间里做模型预测控制。

**这是 2025-2026 年真正的架构分叉：**

- **VLA 路线**：从带动作标签的演示里学一个**策略**，输入观测直接输出动作。
- **世界模型路线**：从被动视频里学**动力学**，运行时在脑内推演、规划出动作。

NVIDIA 在 2026 年 3 月 GTC 上预告的 **GR00T N2** 声称基于一种"world action model"架构，看起来是押注这两条路会合流。但要说清楚：**GR00T N2 目前只是预告，官方说预计年底可用，尚未发布**；同期公布的 Cosmos 3 也标注为"即将推出"。

### 2.9 所以现在到哪一步了？

诚实的评估：

- ✅ **感知**基本解决了（2012 年之后）。
- ✅ **足式运动**基本解决了（2019 年之后，RL 是默认方案）。
- 🔥 **通用操作（manipulation）** 是当前的主战场。能做的事情增长很快，但可靠性远没到工业级。
- ❌ **长程任务、真正的泛化、失败恢复**仍然是开放问题。Figure 那个 4 分钟的洗碗演示之所以是新闻，恰恰说明**连续 4 分钟不出错在今天仍然稀罕**。

对人形机器人的商业化，我建议保持清醒。有几个数字流传很广但我**没能从一手来源核实**：Figure 在宝马工厂的部署规模、Tesla Optimus 的产量（不同来源相互矛盾，从 300 台到 1000 台以上都有）。可核实的是：1X 的 NEO 家用机器人 2025 年 10 月开售（2 万美元或每月 499 美元订阅），加州工厂 2026 年 4 月底开业，但官方页面说目前下线的机器人**优先给内部员工家庭做开发测试**；宇树科技 2026 年 7 月 3 日通过 IPO 注册，**尚未上市交易**。

---

## 三、系统组成：一套具身智能系统有哪些部分

### 3.1 分层架构全景

<div class="mermaid">
graph TB
    subgraph L5["🧠 认知层 · 0.2-10 Hz"]
        C1["任务理解与分解"]
        C2["长程规划"]
        C3["常识与语义推理"]
    end
    subgraph L4["👁️ 感知层 · 10-60 Hz"]
        P1["视觉：检测/分割/位姿"]
        P2["空间：SLAM/建图/点云"]
        P3["本体感觉：关节角/力矩"]
        P4["触觉/力觉"]
    end
    subgraph L3["🎯 决策与运动规划 · 10-100 Hz"]
        D1["技能选择"]
        D2["轨迹生成/避障"]
        D3["抓取位姿生成"]
    end
    subgraph L2["⚙️ 控制层 · 200 Hz - 1 kHz"]
        M1["全身控制 WBC"]
        M2["模型预测控制 MPC"]
        M3["阻抗/导纳控制"]
    end
    subgraph L1["🦾 本体层 · 1-10 kHz"]
        H1["执行器与电流环"]
        H2["传感器与安全"]
    end

    L5 --> L4
    L4 --> L3
    L3 --> L2
    L2 --> L1
    L1 -.反馈.-> L4

    style L5 fill:#c7ceea,color:#3d3556
    style L4 fill:#bee3db,color:#3d3556
    style L3 fill:#b5ead7,color:#3d3556
    style L2 fill:#ffdac1,color:#3d3556
    style L1 fill:#ffd6e0,color:#3d3556
</div>

### 3.2 为什么必须分层：频率决定一切

这是新手最难建立、也最重要的直觉：**不同层的运行频率相差三个数量级，这不是设计品味问题，是物理约束。**

| 层 | 周期 | 为什么是这个频率 |
| --- | --- | --- |
| 认知（大模型） | 0.1-5 秒 | 7B 模型推理一次就要上百毫秒，快不了 |
| 感知 | 16-100 ms | 相机就是 30/60 fps |
| 运动规划 | 10-100 ms | 轨迹优化需要迭代求解 |
| 控制 | 1-5 ms | **机器人会摔倒**——平衡控制慢了就来不及 |
| 电流环 | 微秒级 | 电机物理特性决定 |

一个人形机器人从站立到摔倒只有几百毫秒。**如果让 7B 的大模型直接输出关节力矩，它还没想完就已经躺地上了。**

所以现代架构清一色是"**慢的负责想，快的负责做**"：Helix 的 S2/S1/S0（7-9 Hz / 200 Hz / 1 kHz）、GR00T N1 明确说的"System 1 快动作模型 + System 2 慢 VLM"、π0 的 VLM + 动作专家，本质都是同一个思路。这个思路借用了 Kahneman《思考，快与慢》的双系统比喻。

### 3.3 各部分具体做什么

**① 本体（Hardware / Embodiment）**

自由度（DoF）是核心指标。一个典型的人形机器人：每条腿 6 DoF、每条手臂 7 DoF、灵巧手每只 6-20 DoF，加上腰和头，总共几十到上百。执行器路线主要三条：

| 类型 | 特点 | 代表 |
| --- | --- | --- |
| **电驱（准直驱/QDD）** | 可反驱、控制精细、安静 | 现在的主流，如 ANYmal、Unitree |
| **液压** | 力量大、动态强 | 早期 Atlas、BigDog（噪音太大后被弃用） |
| **串联弹性（SEA）** | 有柔顺性、安全 | ANYmal 早期，难精确建模 |

**"可反驱（backdrivable）"是个关键词**：能被外力推动的关节才能做柔顺控制，才敢和人一起工作。

**② 感知（Perception）**

分两类，别混淆：

- **外感受（exteroception）**：相机、深度相机、LiDAR、麦克风——感知外部世界。
- **本体感觉（proprioception）**：关节编码器、IMU、力矩传感器——感知**自己身体的状态**。

本体感觉是具身智能特有的，也是最容易被软件工程师忽略的。人闭着眼也知道自己手在哪，机器人也一样，而且这部分数据频率高、噪声小、极其可靠。

**③ 认知与决策**

现在基本等于"一个 VLM/LLM + 一个动作模型"。上层负责把"把桌子收拾干净"分解成一串可执行的子任务，下层负责具体怎么动。

**④ 控制**

把期望的运动变成实际的关节指令。核心概念是**阻抗控制**——不去精确控制位置，而是控制"力和位移的关系"，让机器人表现得像个弹簧。这是安全交互的基础：拧螺丝、插插头、和人握手，靠的都不是精确定位，而是恰当的柔顺性。

**⑤ 数据引擎（最被低估的部分）**

我想单独强调这一层，因为教程里几乎从不讲，但它决定项目成败。数据从哪来？

| 来源 | 成本 | 质量 | 说明 |
| --- | --- | --- | --- |
| **真机遥操作** | 极高 | 最好 | 人真的去操作机器人。ALOHA、GELLO、VR 遥操 |
| **仿真** | 低 | 有 sim2real 差距 | 可以并行几十万个环境 |
| **人类视频** | 极低 | 需要重定向 | 互联网上无限量，但没有动作标签 |
| **跨本体数据** | 已有 | 需对齐 | Open X-Embodiment |
| **自主练习 + 纠错** | 中 | 最贴合部署 | π\*0.6 的 RECAP 路线 |

**这一层的选择，某种程度上就是各家技术路线的差别。**

### 3.4 sim-to-real：绕不开的鸿沟

仿真里能跑不代表真机上能用，差距来自：接触和摩擦模型不准、执行器动力学（延迟、齿轮间隙、发热）、传感器噪声、光照和材质差异。

三种主流对策：

1. **域随机化**：把仿真参数随机到覆盖现实（前面讲过）。
2. **系统辨识**：把真机的执行器特性测出来，学一个模型补进仿真（ANYmal 那篇的关键）。
3. **真机微调**：仿真预训练 + 少量真机数据微调。

---

## 四、软件知识：工程师真正会用到的技术栈

### 4.1 全景

<div class="mermaid">
graph TB
    subgraph A["应用与学习层 · Python"]
        A1["LeRobot / openpi<br/>Isaac-GR00T"]
        A2["PyTorch / JAX / Warp"]
        A3["RL: RSL-RL / rl_games / skrl"]
    end
    subgraph B["仿真层"]
        B1["MuJoCo / MJX"]
        B2["Isaac Sim + Isaac Lab"]
        B3["Gazebo / Genesis / Drake"]
        B4["Newton 物理引擎"]
    end
    subgraph C["中间件 · ROS 2"]
        C1["节点/话题/服务/动作"]
        C2["DDS 或 Zenoh"]
        C3["MoveIt 2 / Nav2 / ros2_control"]
    end
    subgraph D["实时控制层 · C++"]
        D1["MPC: acados / OCS2"]
        D2["动力学: Pinocchio"]
        D3["PREEMPT_RT 实时内核"]
    end

    A --> B
    A --> C
    C --> D

    style A fill:#c7ceea,color:#3d3556
    style B fill:#bee3db,color:#3d3556
    style C fill:#b5ead7,color:#3d3556
    style D fill:#ffdac1,color:#3d3556
</div>

### 4.2 中间件：ROS 2

**ROS 不是操作系统**，是一套通信中间件加工具链。它解决的问题是：让几十个进程（相机驱动、SLAM、规划器、控制器）以标准方式互相通信。

核心概念就六个，一小时能学会：

| 概念 | 说明 |
| --- | --- |
| **Node（节点）** | 一个进程/功能单元 |
| **Topic（话题）** | 发布订阅，异步多对多，如相机图像流 |
| **Service（服务）** | 请求响应，同步一对一 |
| **Action（动作）** | 长时间任务，带进度反馈和取消，如"导航到某点" |
| **Parameter** | 运行时可调配置 |
| **tf2** | 坐标变换树——**机器人开发中最常打交道的东西** |

**版本怎么选（这点网上教程普遍过期）：**

| 发行版 | 发布 | 支持到 | 建议 |
| --- | --- | --- | --- |
| **Lyrical Luth** | 2026-05-22 | 2031-05 | 最新 LTS，但生态还没跟上 |
| Kilted Kaiju | 2025-05 | 2026-11 | 快 EOL 了 |
| **Jazzy Jalisco** | 2024-05 | 2029-05 | **新项目建议选这个** |
| Humble Hawksbill | 2022-05 | 2027-05 | 存量项目 |

**为什么推荐 Jazzy 而不是最新的 Lyrical？** 因为生态有滞后。截至 2026 年 6-7 月的证据，MoveIt 2 在 Rolling 和 Lyrical 上还没有发布（Universal Robots 的提交注释里直接写着"MoveIt isn't available on rolling and lyrical right now"），Nav2 的 API 文档也只覆盖到 Kilted 和 Rolling。**新 LTS 出来后等半年到一年再上，是这个生态里的常识。**

**ROS 1 已经死了**：最后一个版本 Noetic 于 **2025 年 5 月 31 日** EOL，ROS Wiki 转为归档状态。还在学 ROS 1 的教程可以直接跳过了。

**一个值得关注的变化：Zenoh。** DDS 的服务发现依赖 UDP 组播，而很多企业网络出于安全考虑禁用组播，大型 WiFi 环境也常常抑制它——失败模式还很隐蔽：节点就是互相发现不了，不报错。Zenoh 用 gossip + 组播双路发现，能跨子网路由，也没有"QoS 不兼容"这种概念。它从 Kilted 起成为 **Tier 1** 并随二进制包分发。默认仍是 Fast DDS，但**如果你在企业网络里被 ROS 2 的发现问题折磨过，Zenoh 值得一试**。

### 4.3 仿真器：怎么选

这是软件选型里最容易踩坑的地方，因为**信息过期率极高**。先说三条最重要的：

> **⚠️ Isaac Gym 已经归档了。** `isaac-sim/IsaacGymEnvs` 仓库处于 archived 状态，最后提交在 2024 年 10 月。OmniIsaacGymEnvs 也已被取代。继任者是 **Isaac Lab**。网上大量教程还在教 Isaac Gym，别跟着学。

| 仿真器 | 当前版本 | 协议 | 最适合 |
| --- | --- | --- | --- |
| **MuJoCo** | 3.11.0 | Apache-2.0 | 实时控制、MPC、遥操作（为**低延迟**优化） |
| **MJX / MuJoCo Warp** | 3.11.0 | Apache-2.0 | RL 训练吞吐（为**吞吐量**优化） |
| **Isaac Sim 6.0 + Isaac Lab** | Lab 2.3.x 稳定 | Apache-2.0 + 附加条款 | 照片级渲染、合成数据、大规模 RL |
| **Gazebo** | Jetty（2025-09 LTS） | Apache-2.0 | ROS 原生集成、传感器仿真 |
| **Genesis World** | 1.2.3 | Apache-2.0 | 多物理场（柔体/流体/颗粒） |
| **Drake** | 1.55.0 | BSD-3 | 模型化设计、接触隐式轨迹优化、形式化验证 |
| **ManiSkill / SAPIEN** | 3.0.1 | Apache-2.0 | 铰接物体操作、标准 benchmark |
| **PyBullet** | 3.2.7 | Zlib | ⚠️ 基本进入维护模式，新项目别用 |

几个要点：

**MuJoCo 现在分裂成了三个东西**，选错会很痛苦：原生 MuJoCo（CPU，为**每步延迟**优化，适合实时控制）、**MJX-JAX**（支持自动微分，跨 NVIDIA/AMD/Apple/TPU）、**MJX-Warp**（仅 NVIDIA，**不支持自动微分**，但能处理动态数量的接触点，适合接触密集的大规模 RL）。官方文档诚实地指出 MJWarp 在**超过 60 自由度后性能会明显下降**。

**Isaac Sim 的协议要看清楚**：GitHub 上的源码是 Apache-2.0，但构建和运行需要 Omniverse Kit SDK 和素材，那部分是另一份 NVIDIA 许可。**个人研究没问题，重新分发或对外提供服务会触发企业许可要求。**

**Newton 是值得关注的新东西**：由 Disney Research、Google DeepMind、NVIDIA 联合发起，托管在 **Linux Foundation** 下，Apache-2.0，以 MuJoCo Warp 为主要后端、原生支持 OpenUSD。v1.0 在 2026 年 3-4 月发布，目前 1.4.0。它有可能成为跨厂商的公共物理层。

**关于 Genesis，说点公道话。** 2024 年 12 月它带着"430,000 倍实时速度"的口号刷屏，随后 ManiSkill 的维护者 Stone Tao 审计了 benchmark 代码，发现：用的是最激进的物理参数（和官方教程不一致）、执行一个动作后跟着 999 步空动作（让求解器提前退出）、**关闭了机器人自碰撞**、**完全没开渲染**。修正后在 4090 上，Franka 的性能从 4300 万 FPS 掉到约 29 万 FPS——**差了 150 倍**，实际比 Isaac Lab 和 ManiSkill 慢 3 到 10 倍。

但**用 2024 年的事否定 2026 年的 Genesis 也不公道**。它现在改名 Genesis World，有公司支持，1.0 版本是个实打实的工程成果：刚体+FEM+MPM+PBD 统一多物理场、三种可切换的接触耦合器、自研的 Quadrants 编译器（Python kernel 编译到 CUDA/ROCm/Metal/Vulkan/x86/ARM64 并支持反向自动微分）。**公允的说法是：工程能力强，早期营销过头，现在正在成熟——但性能务必自己按你的负载实测。**

### 4.4 学习框架：从 LeRobot 开始

**如果你只想记住一个仓库，记 [LeRobot](https://github.com/huggingface/lerobot)。** Hugging Face 维护，**26.2k stars**，Apache-2.0，当前 v0.6.0，纯 PyTorch。它现在是开源机器人学习事实上的中心。

内置的策略几乎覆盖了本文提到的所有算法：

- **模仿学习**：ACT、Diffusion Policy、VQ-BeT、Multitask DiT
- **强化学习**：HIL-SERL、TDMPC
- **VLA**：π0、π0-FAST、π0.5、**GR00T N1.7**、SmolVLA、XVLA 等
- **世界模型**（v0.6.0 新增）：VLA-JEPA、FastWAM 等

命令行就四个：`lerobot-train`、`lerobot-eval`、`lerobot-record`、`lerobot-rollout`。

**一个具体的量级参考**：HF 自家的 **SmolVLA** 是 450M 的基础模型，官方文档建议**约 50 条演示**就能微调，单张 A100 上 2 万步大约 **4 小时**。配合几千块的 **SO-ARM100/SO-101** 开源机械臂（6.9k stars），这是目前个人入门成本最低的路径。

**其他重要仓库：**

| 仓库 | Stars | 状态 |
| --- | --- | --- |
| `Physical-Intelligence/openpi` | 13.0k | **活跃**，开源了 π0 / π0-FAST / π0.5 |
| `NVIDIA/Isaac-GR00T` | 7.7k | **活跃**，GR00T N1.7 |
| `openvla/openvla` | 6.7k | ⚠️ 冻结（2025-03 后无更新） |
| `real-stanford/diffusion_policy` | 4.4k | ⚠️ 冻结（2024-12 后无更新） |
| `tonyzhaozh/act` | 2.1k | ⚠️ 冻结（2024-07 后无更新） |

**注意后三个都是冻结的研究代码**。这不是说它们不能用，而是说：**论文仓库是快照，真正的活跃开发在 LeRobot 里。** 想跑 ACT 或 Diffusion Policy，用 LeRobot 的实现，不要 clone 原始仓库。

**RL 库选型**：足式运动用 **RSL-RL**（ETH 出品，2.8k stars，事实标准）；追求吞吐用 **rl_games**；想同时支持 PyTorch 和 JAX 用 **skrl**；教学和 baseline 用 **Stable-Baselines3**（13.6k stars）。

**数据格式要注意**：Open X-Embodiment 原本用 Google 的 **RLDS** 格式（基于 TFRecord），但 **`google-research/rlds` 仓库已归档**（2024 年 9 月后无更新）。在 PyTorch 主导的生态里，**LeRobotDataset v3.0**（Parquet 存元数据和动作 + MP4 存视频）已经成为事实上的继任者。

### 4.5 感知栈

**SLAM 这块，维护状态比 star 数重要得多：**

| 系统 | Stars | 协议 | 状态 |
| --- | --- | --- | --- |
| **RTAB-Map** | 3.9k | BSD 类 | ✅ **活跃**，且已支持到 Lyrical |
| ORB-SLAM3 | 8.9k | **GPL-3.0** | ⚠️ 2024-07 后停更，且 GPL 对商用是障碍 |
| Cartographer | 7.9k | Apache-2.0 | ❌ **官方声明不再维护** |

Cartographer 的 README 说得很直白："Cartographer is no longer actively maintained." **实际项目里 RTAB-Map 是更稳妥的选择。**

**视觉基础模型**：CLIP（34k）、DINOv2/DINOv3、SAM 系列。这里有个更新值得注意——**SAM 3**（2025-11-19）引入了"可提示概念分割"，可以用一个名词短语（如"黄色校车"）分割出所有匹配实例并保持 ID 稳定；**SAM 3.1**（2026-03-27）又把多目标跟踪做了优化，128 个物体时在单张 H100 上快约 7 倍。MoveIt Pro 已经用 SAM3 替换了原来的 CLIPSeg。

⚠️ 顺带提醒：**Grounding DINO 自 2024 年 8 月起没再更新**，虽然仍被广泛使用，但 SAM 3 的原生文本提示某种程度上已经覆盖了"Grounding DINO + SAM"这个经典组合。

**再澄清一个流传很广的误解：RealSense 没有停产。** 它在 **2025 年 7 月 11 日从 Intel 分拆独立**，完成了 5000 万美元 A 轮融资。最有力的证据是 GitHub 组织都改名了——`IntelRealSense/librealsense` 现在跳转到 `realsenseai/librealsense`，最近提交是 2026 年 7 月。误解的源头大概是 2021 年 8 月 Intel 砍掉了 LiDAR 和跟踪相机产品线，那是分拆四年前的事。

### 4.6 控制与规划

这一层基本是 C++ 的天下：

| 工具 | 协议 | 用途 |
| --- | --- | --- |
| **Pinocchio** | BSD-2 | 刚体动力学（RNEA/ABA/CRBA）+ **解析导数**，是整个控制栈的地基 |
| **acados** | BSD 类 | 嵌入式实时非线性 MPC，openpilot 在用 |
| **OCS2** | BSD-3 | ETH 出品，足式机器人实时 MPC 的标准工具箱 |
| **CasADi** | ⚠️ **LGPL-3.0** | 符号微分与优化建模 |
| **OMPL** | BSD-3 | 采样式运动规划，MoveIt 的默认后端 |

**CasADi 是 LGPL-3.0，这是本节里限制最强的协议，商用前务必确认。**

**cuRobo 和 cuMotion 已经分家了**，这点容易混淆：`NVlabs/curobo` 现在是**研究代码库**；`nvidia-isaac/cumotion` 是**产品化版本**（C++ 实现 + Python 绑定，1.1.0 发布于 2026-04）。Isaac ROS 4.4 起，`isaac_ros_manipulation` 用的是 cuMotion 而非 cuRobo。

### 4.7 描述格式：URDF、MJCF、USD

| 格式 | 归属 | 描述范围 |
| --- | --- | --- |
| **URDF** | ROS | 单个机器人：连杆、关节、惯量、几何 |
| **SDF** | Gazebo | 整个世界：多模型、光照、传感器 |
| **MJCF** | MuJoCo | 富接触模型和执行器模型 |
| **USD / OpenUSD** | Pixar → AOUSD | 通用 3D 场景图，Isaac Sim / Newton 的原生格式 |

**哪个会赢？2026 年的诚实答案是：都没赢，但 ROS 生态已经开始正式接纳 USD。**

证据是 **REP-0158**《OpenUSD Conventions for Simulation Asset Interoperability》。这份提案自己的措辞很坦率——它一方面说 OpenUSD 已成为行业标准、ROS 需要跟上，另一方面直言：

> *"OpenUSD and robotics XML formats (URDF, SDF, MJCF) are fundamentally mismatched paradigms. Because OpenUSD lacks native schemas for domain-specific data, conversions are inherently lossy."*
>
> （OpenUSD 和机器人 XML 格式在范式上根本不匹配。由于 OpenUSD 缺少领域数据的原生 schema，转换本质上是有损的。）

**实践建议**：把 **URDF 当作唯一权威源**（厂商发的是它，ROS 吃的也是它），按需转换到 MJCF 或 USD，**并预留时间手工重调接触参数和执行器模型**。典型转换保真度约 80%，URDF 根本无法表达接触刚度阻尼，MJCF 能表达的齿轮比和电机特性 URDF 也没有。

### 4.8 语言分工与实时性

**Python 和 C++ 的分工不是品味问题，是被频率决定的：**

| 层 | 周期 | 语言 |
| --- | --- | --- |
| VLA / 基础模型 | 秒级 | Python |
| ROS 2 应用节点 | 50 ms - 秒 | Python 或 C++ |
| ROS 2 控制器 | 5-20 ms | **C++** |
| 单片机固件 | 0.1-10 ms | C/C++ |
| 电机驱动 | 微秒级 | 固件 |

ROS 2 官方的实时文档明确假定使用 C++ 接口。**`rclpy` 属于软实时的监督和编排层，不该出现在控制回路里。**

**实时性**靠 Linux 的 `CONFIG_PREEMPT_RT`（现已进主线，不再是外挂补丁）。它让内核大部分可抢占、中断线程化、自旋锁换成优先级继承互斥锁。但它**不能**消除硬件延迟、驱动缺陷、缺页中断和糟糕的应用设计。`ros2_control` 的控制器管理器跑在 `SCHED_FIFO` 优先级 50 上，所以这个循环里的抖动会直接传导出去。

**Rust 的现状值得单说**，因为它常被过度乐观地宣传。事实是：Rust **赢下了基础设施层**——Zenoh 是 Rust 写的，iceoryx2（共享内存 IPC）是 Rust 重写的，可视化工具 Rerun（11.2k stars）也是。在应用层，`rclrs` 已于 2026 年 1 月发布 v0.7.0，而且 **ROS 2 Lyrical 把 `rosidl_generator_rs` 作为默认生成器之一**，这是个实质性的里程碑。但仓库自己仍然写着"客户端库仍在快速演进，目前不提供稳定性保证"，而且基于 Tokio 的执行器有明确限制：1 kHz 定时器实测只能跑到约 600 Hz。

**公允的结论：Rust 拿下了中间件和工具层，C++ 仍然牢牢占据实时控制路径。**

### 4.9 一条务实的入门路径

给想真正动手的人：

<div class="mermaid">
graph LR
    S1["1. Python + PyTorch<br/>先有的基础"] --> S2["2. MuJoCo<br/>装上就能跑<br/>理解仿真和控制"]
    S2 --> S3["3. LeRobot<br/>跑通 ACT / Diffusion Policy<br/>用公开数据集"]
    S3 --> S4["4. 买一套 SO-101<br/>采自己的数据<br/>体会真机的坑"]
    S4 --> S5["5. ROS 2 Jazzy<br/>需要做系统集成时再学"]
    S5 --> S6["6. Isaac Lab<br/>需要大规模 RL 时再学"]

    style S1 fill:#b5ead7,color:#3d3556
    style S2 fill:#bee3db,color:#3d3556
    style S3 fill:#ffdac1,color:#3d3556
    style S4 fill:#c7ceea,color:#3d3556
    style S5 fill:#e2f0cb,color:#3d3556
    style S6 fill:#ffd6e0,color:#3d3556
</div>

**关键建议：不要从 ROS 2 开始。** 很多人一上来啃 ROS 2 教程，学了两个月的话题和服务，却没训练过一个策略。ROS 2 是**系统集成**工具，当你需要把多个模块拼起来跑在真机上时它才有价值。**先用 MuJoCo + LeRobot 把"数据 → 训练 → 评估"这个闭环跑通，你对这个领域的理解会快得多。**

---

## 五、名词速查表

### 5.1 范式与架构

| 名词 | 解释 |
| --- | --- |
| **Embodied AI（具身智能）** | 智能体有物理身体，通过与真实环境交互来感知、学习、行动 |
| **Embodiment（本体）** | 智能体的物理形态。"跨本体"指同一模型能控制不同形态的机器人 |
| **VLA（Vision-Language-Action）** | 视觉-语言-动作模型。输入图像和语言指令，直接输出动作。当前主流范式 |
| **VLM（Vision-Language Model）** | 视觉语言模型。VLA 通常以它为骨干 |
| **World Model（世界模型）** | 能预测"环境会怎么变、我的动作会带来什么"的模型 |
| **Foundation Model（基础模型）** | 大规模预训练、可迁移到多任务的通用模型 |
| **Sense-Plan-Act** | 经典范式：感知→建模→规划→执行。已被证明在开放环境中太慢 |
| **Subsumption Architecture（包容架构）** | Brooks 1986 提出，多层行为模块并行，高层抑制低层输出，不要中央世界模型 |
| **Physical Grounding Hypothesis（物理接地假说）** | Brooks 的主张：智能系统的表征必须扎根于物理世界 |
| **Moravec's Paradox（莫拉维克悖论）** | 对 AI 来说推理容易、感知运动难 |
| **System 1 / System 2（快慢系统）** | 借自 Kahneman。慢系统负责理解和规划，快系统负责高频动作输出 |
| **Affordance（可供性）** | 物体提供的操作可能性。杯子的"可抓握性"就是一种 affordance |

### 5.2 学习方法

| 名词 | 解释 |
| --- | --- |
| **Imitation Learning（模仿学习）** | 从人类演示中学。当前操作任务的主力方法 |
| **Behavior Cloning（行为克隆，BC）** | 最简单的模仿学习：把"观测→动作"当监督学习做 |
| **Compounding Error（误差累积）** | BC 的核心难题。小误差让机器人偏离训练分布，越偏越远 |
| **DAgger** | 让专家在机器人自己跑出来的状态上标注，缓解误差累积 |
| **Action Chunking（动作分块）** | 一次预测一整段未来动作而非单步，缩短有效决策长度。ACT 提出，现已成标配 |
| **ACT** | Action Chunking Transformer，配合 ALOHA 硬件，2023 |
| **Diffusion Policy** | 用扩散模型生成动作序列，能表达多模态动作分布，2023 |
| **Flow Matching（流匹配）** | 扩散的一种变体，采样更快。π0 系列用它输出连续动作 |
| **RL（强化学习）** | 试错学习。足式运动的默认方案，操作任务上正在回归 |
| **HIL-SERL** | Human-in-the-Loop 的真机 RL 方法 |
| **Offline RL** | 从固定数据集学，不与环境交互 |
| **Cross-Embodiment（跨本体）** | 用多种机器人的数据一起训练，能产生正迁移 |
| **Zero-shot / Few-shot** | 零样本 / 少样本。不训练或只用极少样本就能做新任务 |

### 5.3 仿真与迁移

| 名词 | 解释 |
| --- | --- |
| **Sim-to-Real（sim2real）** | 把仿真中训练的策略迁移到真机 |
| **Reality Gap（现实鸿沟）** | 仿真与现实的差距。主要来自接触模型、执行器动力学、传感器噪声 |
| **Domain Randomization（域随机化）** | 随机化仿真参数，让现实看起来只是训练分布的一个样本 |
| **ADR（自动域随机化）** | 自动递增随机化难度，OpenAI 2019 提出 |
| **System Identification（系统辨识）** | 测量真机参数并反哺仿真模型 |
| **Digital Twin（数字孪生）** | 真实系统的高保真虚拟副本 |
| **Photorealistic Rendering** | 照片级渲染，为视觉策略提供逼真训练图像 |
| **Parallel Environments（并行环境）** | GPU 上同时跑成千上万个仿真实例。Helix 02 用了 20 万个 |

### 5.4 硬件与控制

| 名词 | 解释 |
| --- | --- |
| **DoF（Degrees of Freedom，自由度）** | 独立可动关节数。人形机器人通常几十个 |
| **End-Effector（末端执行器）** | 机械臂末端的手爪或工具 |
| **Proprioception（本体感觉）** | 机器人对自身状态的感知：关节角、速度、IMU |
| **Exteroception（外感受）** | 对外部世界的感知：相机、LiDAR |
| **Kinematics（运动学）** | 只讲位置速度不讲力。**正运动学**由关节角算末端位姿，**逆运动学（IK）**反过来 |
| **Dynamics（动力学）** | 涉及力、力矩、惯量的运动描述 |
| **Backdrivable（可反驱）** | 关节能被外力推动。柔顺控制和人机安全的前提 |
| **Impedance Control（阻抗控制）** | 不控制精确位置，而是控制力与位移的关系，让机器人像弹簧 |
| **MPC（模型预测控制）** | 用模型预测未来若干步，滚动优化。足式机器人的核心方法 |
| **WBC（Whole-Body Control，全身控制）** | 统一协调所有关节完成多个目标（保持平衡的同时伸手取物） |
| **Teleoperation（遥操作）** | 人远程操控机器人。当前采集训练数据的主要方式 |
| **Leader-Follower（主从）** | 遥操作方式：人操作小的主臂，大的从臂跟随。ALOHA 用的就是这个 |
| **Loco-manipulation** | 移动与操作的结合——边走边干活，比单独任何一项都难 |
| **Dexterous Manipulation（灵巧操作）** | 多指手的精细操作，如手内翻转物体 |
| **Compliance（柔顺性）** | 遇到外力时"让一下"的能力，安全交互的基础 |

### 5.5 软件与数据

| 名词 | 解释 |
| --- | --- |
| **ROS 2** | 机器人通信中间件与工具链。注意它不是操作系统 |
| **DDS** | ROS 2 默认的底层通信标准 |
| **Zenoh** | ROS 2 的新通信后端，解决 DDS 在企业网络中的发现难题 |
| **tf2** | ROS 的坐标变换树，管理各个坐标系之间的关系 |
| **URDF / MJCF / USD** | 机器人和场景的描述格式，分别属于 ROS / MuJoCo / Omniverse 生态 |
| **Open X-Embodiment（OXE）** | 跨机构跨本体的大规模机器人数据集，2023 |
| **LeRobot** | Hugging Face 的开源机器人学习框架，当前生态中心 |
| **RLDS / LeRobotDataset** | 机器人数据集格式。前者已归档，后者是继任者 |
| **PREEMPT_RT** | Linux 实时补丁/配置，硬实时控制的前提 |
| **Isaac Lab** | NVIDIA 的机器人学习框架，Isaac Gym 的继任者 |
| **MJX** | MuJoCo 的加速版本，用于大规模并行 RL 训练 |

---

## 六、总结

### 6.1 三条主线

回看七十年，其实就是三个问题被逐一攻克：

<div class="mermaid">
graph TB
    Q1["问题一：怎么看懂世界?"] --> A1["✅ 2012 年后被深度学习解决"]
    Q2["问题二：怎么控制身体?"] --> A2["✅ 2019 年后强化学习成为默认方案<br/>足式运动基本解决"]
    Q3["问题三：怎么理解任务、泛化到新场景?"] --> A3["🔥 2022 年至今的主战场<br/>答案是借用互联网知识"]

    style Q1 fill:#c7ceea,color:#3d3556
    style Q2 fill:#c7ceea,color:#3d3556
    style Q3 fill:#c7ceea,color:#3d3556
    style A1 fill:#b5ead7,color:#3d3556
    style A2 fill:#b5ead7,color:#3d3556
    style A3 fill:#ffdac1,color:#3d3556
</div>

**图灵那两条路最终合流了**：正是互联网规模的语言和视觉能力，让感官身体这条路第一次变得可行。

### 6.2 核心要点

- **具身智能难在物理不可逆和数据稀缺**，不难在模型本身。语言模型能白嫖互联网文本，机器人数据得一条条采——这个差异决定了这个领域的一切技术选择。
- **分层是物理约束不是设计品味**。认知层秒级、控制层毫秒级，相差三个数量级。让大模型直接输出关节力矩，机器人会在它想完之前摔倒。
- **VLA 是当前主流范式**，把动作当成 token 或用流匹配生成，靠视觉语言模型带来的世界知识实现泛化。
- **世界模型是正在成形的另一条路**：不学策略，学动力学，运行时在脑内推演。V-JEPA 2 用不到 62 小时机器人视频做到零样本跨实验室部署，是这条路最有说服力的证据。
- **软件栈上，先学 MuJoCo + LeRobot，不要从 ROS 2 开始**。ROS 2 是集成工具，等你需要集成时再学。
- **这个领域的信息过期极快**。Isaac Gym 归档了、ROS 1 死了、Cartographer 停维护了、几个经典论文仓库都冻结了、RealSense 其实是分拆而非停产——**看教程先看日期，看仓库先看最后提交时间。**

### 6.3 最后

具身智能现在的位置，有点像 2018 年的 NLP：范式刚刚统一（那时是 Transformer + 预训练，现在是 VLA + 跨本体数据），能力增长很快，但离可靠还很远。

Brooks 在 1990 年写下"**世界就是它自己最好的模型**"时，是在反对给机器人塞进一个符号世界模型。三十六年后，我们绕了一大圈，用互联网上几十亿张图片和几万亿字文本，给机器人塞进了一个**统计意义上的**世界模型——然后发现这次它管用了。

有意思的是，莫拉维克那句话至今依然成立：**推理仍是那层薄薄的表皮，感知和运动仍是最难的部分。** 只不过这一次，我们终于找到了一条能把十亿年的进化经验，压缩进几百小时演示数据的路。

> 如果你想动手，我的建议是：装个 MuJoCo，跑一遍 LeRobot 的入门示例，用一个下午的时间，你对这篇文章的理解会超过读十遍。

---

*本文所有版本号、日期与数据核实于 2026 年 7 月 29 日。这个领域变化很快，阅读时请留意时效。*
