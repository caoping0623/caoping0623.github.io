---
title: "整车域控与诊断路由全景：从分布式 ECU 到 DoIP 诊断网络"
date: 2026-07-30
categories: [车载工程]
tags: [诊断, 域控, DoIP, UDS, 网关, CAN, 以太网, 分区架构, 诊断路由]
excerpt: "从分布式 ECU、域集中到分区/中央计算，整车电子电气架构在变，诊断入口与路由也随之重构。本文系统梳理整车域控形态、诊断网关角色、物理/功能寻址、DoIP 实体模型、多总线诊断网络方案、典型路由拓扑，以及域控/智驾/座舱场景下的工程要点。"
---

> 写给车载诊断、网关与 EE 架构工程师。
>
> 今天谈「整车诊断」，已经不能只画一张 OBD 接 CAN 的图。高算力 **域控制器（Domain Controller）**、**中央计算 + 分区控制器（Zonal）**、车载以太网骨干，以及 **DoIP（ISO 13400）** 入口，把诊断从「总线直达」变成了「**有入口、有路由表、有协议转换、有安全门禁**」的网络工程。
>
> 本文尽量细：架构演进 → 域控形态 → 诊断路由原理 → 网络方案 → 典型拓扑 → 工程落地要点。参数与逻辑地址以主机厂规范为准，文中给的是行业通用骨架。

---

## 1. 为什么诊断网络必须跟着 EE 架构一起变

### 1.1 诊断要解决的本质问题

诊断通信的应用层今天几乎统一到 **UDS（ISO 14229）**：读 DID、清 DTC、会话控制、刷写等。  
变的不是「服务长什么样」，而是：

| 问题 | 过去 | 现在 |
| --- | --- | --- |
| Tester 接哪里 | 往往直接挂某条 CAN | 多经 OBD/以太网进 **中央诊断网关** |
| 目标 ECU 在哪 | 同总线或简单跨网关 | 可能在域控后面、分区后面、甚至虚拟机里 |
| 带宽 | CAN/ISO-TP 够用 | 大包刷写、日志拉取需要以太网 |
| 安全 | 相对简单 | 防火墙、Routing Activation、TLS（可选）、SecOC/诊断鉴权 |

所以「整车诊断方案」= **入口策略 + 寻址模型 + 路由策略 + 传输映射 + 安全策略**。

### 1.2 EE 架构三代演进（诊断视角）

<div class="mermaid">
flowchart LR
    A["分布式 ECU<br/>功能=独立控制器"] --> B["域集中<br/>Domain Controller"]
    B --> C["分区/中央计算<br/>Zonal + HPC"]

    A -.-> A1["诊断: 多总线直连<br/>或简单网关透传"]
    B -.-> B1["诊断: 中央网关 + 域内路由"]
    C -.-> C1["诊断: 单 DoIP 入口<br/>跨区/跨 OS 转发"]

    style A fill:#ffdac1,color:#3d3556
    style B fill:#bee3db,color:#3d3556
    style C fill:#b5ead7,color:#3d3556
</div>

| 代际 | EE 特征 | 诊断网络特征 |
| --- | --- | --- |
| **分布式** | 数十上百 ECU，功能分散 | 多诊断总线；网关做 CAN↔CAN 透传；功能寻址靠广播 |
| **域控** | 动力/底盘/车身/座舱/智驾等按功能域集中 | 域控成为域内「计算+部分网关」；整车仍常有 Central Gateway |
| **分区/中央** | 按车身区域汇聚 I/O，中央 HPC 跑服务 | 线束缩短；诊断更倾向 **单一 DoIP Edge**，再路由到分区与遗留总线 |

业界当前大量量产仍是 **「域控为主、分区渐进」** 的混合态；诊断方案必须兼容这种混合。

---

## 2. 整车域控：形态、职责与诊断角色

### 2.1 什么是域控制器

**域控制器**把过去多个功能 ECU 的计算与部分通信集中到一颗（或一组）高算力 SoC/MCU 上，按**功能域**划分，例如：

| 域 | 典型域控 | 主要负载 |
| --- | --- | --- |
| 动力域 | 动力域控 / VCU 升级形态 | 驱动、能量、部分热管理协同 |
| 底盘域 | 底盘域控 | 转向/制动/悬架协同（常与安全 MCU 并存） |
| 车身域 | 车身域控 / BCM 升级形态 | 门锁、灯光、座椅、舒适 |
| 座舱域 | CDC / Cockpit SoC | IVI、仪表、HUD、语音、部分座舱中间件 |
| 智驾域 | ADCU / ADAS Domain Controller | 感知融合、规划控制、传感器接入 |
| 中央网关/车载电脑 | CGW / HPC | 跨域路由、诊断入口、部分车辆服务 |

注意：域控不等于「把安全岛也吃掉」。智驾/底盘常见 **主 SoC + Safety MCU** 异构，诊断上可能是**两个逻辑地址**或主从诊断拓扑。

### 2.2 域控在诊断里的三种角色

同一块硬件，诊断角色可能不同：

| 角色 | 含义 | 例子 |
| --- | --- | --- |
| **Diagnostic Server（被测端）** | 实现 UDS Server，响应会话/DID/DTC/刷写 | 座舱 SoC、ADCU 应用核 |
| **Diagnostic Gateway（路由端）** | 把请求转发到子网 ECU | 车身域控后面挂 LIN 从节点；中央网关后面挂各 CAN |
| **DoIP Entity** | 实现 DoIP：Edge Gateway 或 Node | 中央网关常为 DoIP Gateway；以太网 ECU 可为 DoIP Node |

一块「智驾域控」常见组合：

- 对外：经车载以太网被中央网关路由，或自身具备 DoIP Node
- 对内：对毫米波/摄像头 MCU、安全岛做 CAN/私有链路诊断转发（OEM 定制）

### 2.3 域控 vs 分区控制器（Zonal Controller）

| 维度 | Domain Controller | Zonal Controller |
| --- | --- | --- |
| 划分依据 | **功能**（智驾/座舱/动力…） | **物理区域**（前左/后舱/舱顶…） |
| 线束 | 域内传感器仍可能长线到域控 | I/O 就近接入，骨干以太网互联 |
| 诊断 | 按功能域寻址清晰 | 同一分区内功能混杂，路由表更「地理化」 |
| 趋势 | 当前主流过渡形态 | SDV 中长期方向；诊断常配合动态配置 |

对诊断工程师：分区架构下，「找 ECU」更依赖 **逻辑地址表 + 拓扑数据库**，而不是「它在动力 CAN 上」。

### 2.4 中央网关（CGW）为什么仍是诊断心脏

即使有多个域控，多数主机厂仍保留 **Central Gateway / DoIP Edge Node**，因为：

1. **单一车外入口**：OBD 以太网（Activation Line）+ 可选远程诊断经 T-Box  
2. **多总线汇聚**：CAN/CAN FD/LIN/FlexRay/Ethernet 异构互联  
3. **安全边界**：防火墙、诊断会话鉴权、路由激活控制  
4. **统一逻辑地址空间**：车外 Tester 只认「逻辑地址」，不关心背后是 CAN 还是 ETH  

Bosch 等供应商也明确：中央网关是诊断请求进入各域的关键节点。

---

## 3. 诊断路由：概念、寻址与网关行为

### 3.1 诊断网关的定义

ISO 13400 语境下：

- **DoIP Entity**：实现 DoIP 协议的主机  
  - **DoIP Gateway**：可访问自身，并**把诊断数据路由到车内子网**  
  - **DoIP Node**：主要提供自身访问，**不负责向子网路由 DoIP**  
- **Vehicle Subnetwork**：不直接挂在 IP 网上的车内网络（如 CAN），只能经 Gateway 到达  
- **Diagnostic Gateway（广义）**：物理连接 ≥2 个子网、能在子网间转发诊断报文的节点（含传统 CAN 网关）

一句话：

> **应用层是 UDS；运输层可能是 DoCAN / DoIP / 其他；中间的转发决策叫诊断路由。**

### 3.2 物理寻址 vs 功能寻址

| 类型 | 含义 | 典型用途 | 路由含义 |
| --- | --- | --- | --- |
| **Physical Addressing** | 请求打向**单个** Server | 读特定 DID、刷写、精确清码 | 网关按目标逻辑地址选唯一路径 |
| **Functional Addressing** | 请求打向**一组/全部** Server | `0x10` 扩展会话广播、`0x3E`、部分清 DTC | 网关可能向多总线 **多播/广播**；响应需汇聚回 Tester |

要点（DoIP）：

- 车内非以太网 ECU **没有 IP**，靠 **DoIP Logical Address** 标识  
- DoIP **不支持**用一个 IP 多播打到多个 DoIP Entity；多 Entity 场景下，功能寻址常由 Client **分别单播**到各 Entity，再由各 Gateway 在子网做功能寻址  
- 传统纯 CAN 车：功能寻址更常见为诊断 CAN 上的功能请求 ID 广播

### 3.3 路由表里到底有什么

工程上，诊断路由配置通常包含：

| 字段 | 说明 |
| --- | --- |
| Target Logical Address | 目标 ECU 诊断逻辑地址 |
| Source Logical Address | Tester / 网关侧源地址规则 |
| Outgoing Network | CAN1 / CAN2 / ETH / LIN… |
| Transport | CanTp / DoIP / LinTp… |
| Addressing Type | Physical / Functional |
| Timeout / P2 策略 | 跨网关要叠加延迟 |
| Access Policy | 哪些服务允许远程、哪些仅近场 OBD |
| Protocol Mapping | DoIP ↔ UDS on CAN 的封装/解封装 |

**路由不是简单「帧透传」**：要处理寻址转换、流控差异、功能寻址扇出、响应聚合、会话状态与 TesterPresent 保活。

### 3.4 请求与响应的转发路径

典型路径：

```text
Tester --DoIP/TCP--> DoIP Edge Gateway
       --路由决策--> 子网传输(CanTp/DoIP/...)
       --> Target DCM/UDS Server
Response 原路或按路由规则返回 Tester
```

多层网关时：

```text
Tester → CGW → Domain GW / Zonal GW → Target
```

每一跳都可能：

- 改寻址（逻辑地址 ↔ CAN ID）  
- 改传输（DoIP ↔ ISO-TP）  
- 引入额外延迟与失败点  

### 3.5 诊断路由的几种实现模式

| 模式 | 做法 | 优点 | 代价 |
| --- | --- | --- | --- |
| **透明桥接** | 尽量原样转发诊断帧 | 简单 | 跨异构总线困难 |
| **应用层网关** | 收 UDS，按表转发 | 可控、可过滤服务 | 开发与测试重 |
| **单一 DoIP 入口 + 隧道** | 车外只认 DoIP；车内 CAN 用隧道到遗留 ECU | 刷写带宽高、入口统一 | Edge 成单点关键 |
| **多 DoIP Entity** | 域控各自对外 DoIP | 降低中心负载 | Tester 要管多连接；功能寻址更复杂 |
| **动态诊断控制器** | 配置与路由可 OTA 下发（异构 OS） | 适配 SDV | 需强版本与安全治理 |

2026 年已有面向分区平台的「Dynamic Diagnostic Controller」类方案讨论：在 RTOS/Linux/QNX 混杂、CAN/LIN/DoIP/SOME/IP 并存时，把诊断协议处理上收、配置与镜像解耦。

---

## 4. 诊断网络方案：总线与传输栈

### 4.1 协议分层（务必分清）

<div class="mermaid">
flowchart TB
    APP["应用层: UDS ISO 14229"]
    TP1["传输: ISO-TP 15765-2<br/>DoCAN"]
    TP2["传输: DoIP ISO 13400<br/>DoIP + TCP/UDP"]
    DLL1["数据链路: CAN / CAN FD"]
    DLL2["数据链路: Automotive Ethernet"]
    PHY1["物理: CAN 收发"]
    PHY2["物理: 100/1000BASE-T1 等"]

    APP --> TP1 --> DLL1 --> PHY1
    APP --> TP2 --> DLL2 --> PHY2

    style APP fill:#c7ceea,color:#3d3556
    style TP1 fill:#bee3db,color:#3d3556
    style TP2 fill:#b5ead7,color:#3d3556
</div>

**UDS 不变；变的是下面怎么运。**

### 4.2 常用车载网络在诊断中的角色

| 网络 | 典型速率 | 诊断角色 | 备注 |
| --- | --- | --- | --- |
| **CAN** | 125k–1M | 遗留 ECU 主力诊断总线 | ISO-TP 分帧；负载高时刷写慢 |
| **CAN FD** | 更高数据段速率 | 新域内诊断/刷写增强 | 仍受仲裁与拓扑约束 |
| **LIN** | ~20 kbit/s | 车身子节点诊断（经网关） | 慢；适合简单从节点 |
| **FlexRay** | 10M 级 | 部分底盘/老平台 | 新平台渐少 |
| **Automotive Ethernet** | 100M/1G/… | 骨干、域控、DoIP、日志、SOME/IP | 诊断与业务流量需 QoS/隔离 |
| **无线（经 T-Box）** | 蜂窝 | 远程诊断/远程刷写编排 | 车内仍落到 DoIP/UDS；安全要求更高 |

### 4.3 DoCAN（UDS on CAN）要点

- 寻址：物理请求 ID / 响应 ID；功能请求 ID  
- 传输：ISO-TP（SF/FF/CF/FC），`BS`、`STmin` 影响吞吐  
- 会话：S3Server 超时；TesterPresent 保活  
- 网关：跨 CAN 时做 ID 映射与功能寻址扇出  

适用：绝大多数遗留控制器、安全 MCU、低带宽节点。

### 4.4 DoIP（UDS on IP）要点

ISO 13400 规定 DoIP 实体与网关行为，包括（强制能力摘要）：

- 车辆网络集成与 IP 分配  
- Vehicle Announcement / Discovery  
- 基本状态信息（如诊断电源模式）  
- 连接建立/维持、并发连接控制  
- **到车辆子部件的数据路由**  
- 错误处理（如物理链路断开）  

可选能力包括 TLS、防火墙能力、实体状态监控等（以标准版本与项目选型为准；ISO 13400-2 已有 2025 版更新）。

**常用端口**：UDP/TCP **13400**（业界通行实现）。

#### 4.4.1 典型建链顺序（近场以太网）

<div class="mermaid">
sequenceDiagram
    participant T as Diagnostic Tester
    participant G as DoIP Edge Gateway

    Note over T,G: 链路与激活线就绪 / IP 可达
    T->>G: UDP Vehicle Identification Request
    G-->>T: UDP Vehicle Announcement<br/>VIN / LA / EID 等
    T->>G: TCP Connect :13400
    T->>G: Routing Activation Request
    G-->>T: Routing Activation Response
    T->>G: Diagnostic Message (UDS)
    G-->>T: Diagnostic Message (UDS Response)
</div>

**Routing Activation** 是关键门禁：激活类型可区分常规诊断、刷写、远程诊断等；失败则不应进入业务 UDS。

#### 4.4.2 DoIP 头与逻辑地址

DoIP Diagnostic Message 携带：

- Source Logical Address  
- Target Logical Address  
- UDS Payload  

网关靠 **Target Logical Address** 查路由表，决定发到哪条 CAN/ETH/LIN。

### 4.5 诊断电源、激活线与物理入口

工程规格里必须写清：

| 项目 | 说明 |
| --- | --- |
| OBD 接口 | SAE J1962 等；现代车常含以太网诊断引脚 |
| Ethernet Activation Line | 插上 Tester 才激活车端诊断以太网口（节能与安全） |
| Diagnostic Power Mode | 告知当前是否允许诊断/刷写 |
| KL30/KL15 策略 | 哪些诊断在下电后仍可达（网关常驻） |

### 4.6 安全相关网络方案（诊断侧）

| 机制 | 作用 |
| --- | --- |
| 路由激活鉴权 | 谁允许建立诊断通道 |
| 服务级过滤 | 远程禁止刷写/写敏感 DID |
| VLAN / 防火墙 | 诊断网与娱乐网隔离 |
| TLS（DoIP 可选） | 传输层保护 |
| Security Access (0x27) / 更现代鉴权 | 应用层解锁 |
| 日志与入侵检测 | 异常诊断行为审计 |

远程诊断 = T-Box 隧道到车内 DoIP/网关，**安全策略必须严于近场 OBD**。

---

## 5. 整车典型诊断网络拓扑

### 5.1 拓扑 A：传统中央网关 + 多 CAN（仍大量在售）

```text
OBD-CAN --> CGW --> Powertrain CAN
                 --> Body CAN
                 --> Chassis CAN
```

- 诊断入口：OBD 诊断 CAN  
- 路由：CGW 做 CAN↔CAN  
- 域控：可能尚未出现，或域控只是「大号 ECU」挂在某总线上  

### 5.2 拓扑 B：中央 DoIP 网关 + 域控（当前主流过渡）

<div class="mermaid">
flowchart TB
    TEST["外部 Tester"] -->|ETH DoIP| CGW["Central GW<br/>DoIP Edge"]
    CGW -->|ETH| CDC["座舱域控 CDC"]
    CGW -->|ETH| ADCU["智驾域控 ADCU"]
    CGW -->|CAN FD| VCU["动力域控/VCU"]
    CGW -->|CAN| BODY["车身域控"]
    BODY -->|LIN| LINN["LIN 从节点"]
    ADCU -->|CAN| SMU["Safety MCU"]
    CGW -->|CAN| CHAS["底盘节点"]

    style CGW fill:#b5ead7,color:#3d3556
    style CDC fill:#ffdac1,color:#3d3556
    style ADCU fill:#ffdac1,color:#3d3556
</div>

特征：

- **车外只看见一个 DoIP Gateway**  
- 座舱/智驾走以太网（高带宽刷写、日志）  
- 遗留与安全 MCU 仍在 CAN  
- 车身域控对下再做 LIN 诊断路由  

### 5.3 拓扑 C：分区架构 + 中央 HPC

```text
Tester --DoIP--> HPC/CGW
              |-- ETH --> Zonal Front
              |-- ETH --> Zonal Rear
              |-- ETH --> Zonal Cabin
              `-- 各 Zonal 再接本地 CAN/LIN 执行器
```

特征：

- 诊断路径更长，延迟预算要重新测  
- 逻辑地址数量上升，配置数据库成为核心资产  
- OTA/诊断配置可能随软件定义车辆动态更新  

### 5.4 拓扑 D：多 DoIP Entity（高性能平台）

部分车型让座舱/智驾成为 **DoIP Node**（甚至二级 Gateway）：

- Tester 可对 Edge 路由，或在授权下直连域控 DoIP  
- 优点：降低中心负载、刷写更快  
- 代价：发现流程、功能寻址、防火墙策略更复杂  

### 5.5 远程诊断叠加

```text
云端诊断平台 --> 蜂窝 --> T-Box --> 车内 DoIP/CGW --> Target
```

与近场差异：

- 必须二次鉴权与策略（哪些 SID 允许）  
- 链路抖动大，刷写常改为「先下载包，再本地 UDS」  
- 会话超时与重连策略要单独设计  

---

## 6. 分域看：座舱、智驾、车身的诊断网络差异

### 6.1 座舱域控（CDC / Cockpit SoC）

| 项目 | 常见做法 |
| --- | --- |
| 链路 | 车载以太网；DoIP Node 或经 CGW |
| OS | Android/QNX/Linux，诊断守护进程 + 厂商中间件 |
| 难点 | 大镜像刷写、多分区、应用与中间件版本矩阵 |
| 子节点 | 仪表、音响功放等可能挂 CAN/私有 ETH，需域内路由 |
| 注意 | 诊断与多媒体流量争用：QoS、诊断窗口 |

### 6.2 智驾域控（ADCU）

| 项目 | 常见做法 |
| --- | --- |
| 链路 | 高速 ETH + 多路 CAN/私有相机链路 |
| 诊断对象 | SoC、Safety MCU、部分传感器 ECU |
| 难点 | 异构核、安全状态机、传感器标定数据 |
| 路由 | SoC 侧诊断服务 + 对 MCU 的网关转发（OEM） |
| 注意 | 智驾激活态常禁止高危诊断/刷写（与 OTA Safe State 一致） |

### 6.3 车身/底盘域控

| 项目 | 常见做法 |
| --- | --- |
| 链路 | CAN FD 为主，ETH 渐入 |
| 子网 | LIN 电机/开关面板极多 |
| 路由 | 域控=诊断 Gateway 的高发区 |
| 注意 | 功能寻址清码范围、抑制通信（0x28）对总线的影响 |

---

## 7. 诊断通信时序与跨网关时序预算

### 7.1 近场完整会话（概念）

1. 物理连接与 Activation  
2. 车辆发现（UDP）  
3. TCP 连接 + Routing Activation  
4. `0x10` 会话控制（物理或功能）  
5. 业务服务（`0x22/0x19/0x27/...`）  
6. 刷写则进入 Programming 路径（见前篇 OTA 文）  
7. 释放连接  

### 7.2 延迟叠加（数量级直觉）

| 路径 | 量级直觉 |
| --- | --- |
| Tester → DoIP Gateway（以太网） | 亚毫秒～数毫秒级 |
| Gateway → CAN ECU（ISO-TP 多帧） | 每段数毫秒～十余毫秒，视块大小与总线负载 |
| Gateway → LIN | 可达数十毫秒级 |

跨两级网关时，P2/P2* 与「功能寻址等谁回」的策略必须按**最慢支路**设计，否则误判超时。

### 7.3 TesterPresent 与会话保持

- Programming / Extended 会话中，Master/Tester 需周期性 `0x3E`  
- 网关若终结会话，背后子网 ECU 可能已掉回默认会话——**路由节点也要有会话策略**  

---

## 8. 工程落地清单：做一版「可量产」诊断网络方案要交什么

### 8.1 必备交付物

| 交付物 | 内容 |
| --- | --- |
| 诊断拓扑图 | 入口、网关层级、各总线、域控位置 |
| 逻辑地址分配表 | 每个 Server 唯一 LA；功能地址定义 |
| 路由矩阵 | Target LA → 出接口/传输/寻址类型/权限 |
| 时序参数表 | P2、P2*、S3、ISO-TP、DoIP 超时 |
| 服务访问策略 | 近场/远程/刷写分别允许的 SID |
| 安全策略 | Routing Activation、防火墙、鉴权 |
| 故障注入用例 | 断网、地址错误、功能寻址风暴、网关过载 |
| 刷写带宽评估 | 各 Target 路径实测吞吐 |

### 8.2 常见坑

1. **只测物理寻址，不测功能寻址扇出** → 全车清码/会话异常  
2. **逻辑地址冲突或重复** → 路由黑洞/错路由  
3. **DoIP 通了但 CAN 侧 ISO-TP 参数不匹配** → 大包失败  
4. **远程诊断放开刷写** → 重大安全事故隐患  
5. **域控当 Gateway 却无诊断负载预算** → 高峰丢响应  
6. **分区改造后仍用旧超时** → 误报通讯失败  
7. **座舱/智驾虚拟化后诊断只打到宿主** → 客端 DTC 不可见  

### 8.3 与 AUTOSAR 的关系（简表）

| 栈 | 模块线索 |
| --- | --- |
| Classic | DCM、CanTp、Dcm、PduR、Gw、DoIP（按版本） |
| Adaptive | `ara::diag`、DoIP Server、向 Classic 的转发 SWC |

混合架构下，Adaptive 域控收 DoIP，再经 CAN 隧道打到 Classic ECU，是培训与实车中的高频模式。

---

## 9. 一张总览：把「域控 + 诊断路由 + 网络方案」串起来

<div class="mermaid">
flowchart TB
    subgraph Access["车外入口"]
      OBD["OBD ETH/CAN"]
      REM["远程 T-Box"]
    end

    subgraph Edge["边缘诊断控制"]
      DOIP["DoIP Edge / CGW<br/>路由激活·防火墙·路由表"]
    end

    subgraph Domains["域控与子网"]
      D1["座舱域控"]
      D2["智驾域控"]
      D3["车身/底盘/动力域控"]
      Z["分区控制器"]
    end

    subgraph Legacy["遗留与执行器"]
      CAN["CAN/CAN FD ECU"]
      LIN["LIN 节点"]
      SMU["Safety MCU"]
    end

    OBD --> DOIP
    REM --> DOIP
    DOIP --> D1
    DOIP --> D2
    DOIP --> D3
    DOIP --> Z
    D2 --> SMU
    D3 --> CAN
    D3 --> LIN
    Z --> CAN
    Z --> LIN

    style DOIP fill:#b5ead7,color:#3d3556
    style Access fill:#c7ceea,color:#3d3556
    style Domains fill:#ffdac1,color:#3d3556
    style Legacy fill:#ffd6e0,color:#3d3556
</div>

**设计口诀：**

1. **入口尽量少而强**（安全与可测性）  
2. **地址全局唯一**（逻辑地址是身份证）  
3. **路由可配置、可审计**（SDV 必备）  
4. **传输按带宽选型**（大包走 ETH/DoIP，遗留走 CAN）  
5. **跨网关按最慢链路定超时**  

---

## 10. 结语

整车域控让「功能计算」集中，也让「诊断可达性」变成网络问题。  
今天合格的诊断方案，不只是 DCM 里堆服务，而是：

> **用 DoIP/中央网关管入口，用逻辑地址与路由表管到达，用多总线传输栈管搬运，用安全策略管边界。**

对座舱与智驾高算力节点，以太网诊断已是默认选项；对海量车身执行器，CAN/LIN + 域内网关仍会长期存在。工程师真正要吃透的，是**异构网络上的统一 UDS 体验**如何被路由层可靠地拼出来。

若你正在写主机厂《整车诊断网络规范》，建议以本文第 8 章交付物为目录，把逻辑地址表与路由矩阵做成受控基线——这比再讲一遍 `0x22` 更接近量产成败。

---

*内容结合 ISO 14229 / ISO 13400 的公开框架、以及域控/分区/中央网关的行业通用实践整理。具体逻辑地址、路由策略、激活类型与安全等级以主机厂诊断规范为准。*
