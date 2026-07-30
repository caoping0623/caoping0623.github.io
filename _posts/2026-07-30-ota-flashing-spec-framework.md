---
title: "智能座舱 SoC 与智驾域控：标准 OTA 刷写技术规格书框架"
date: 2026-07-30
categories: [车载工程]
tags: [OTA, UDS, DoIP, GB44496, UN R156, 诊断, 座舱, 域控, 刷写]
excerpt: "面向智能座舱 SoC 与自动驾驶域控，给出一份可直接落规格书的 OTA 刷写技术框架：云端 / OTA Master / Target ECU 职责边界，UDS over DoIP/CAN 拓扑，GB 44496-2024 与 UN R156 安全合规，完整 UDS 时序，以及超时、断电、校验失败下的状态机与回滚策略。"
---

> 本文面向车载 OTA / 诊断架构工程师，输出的是一份**可直接改写成《OTA 刷写技术规格书》目录与条款骨架**的框架，范围聚焦两类高算力节点：
>
> 1. **智能座舱 SoC**（IVI / Cockpit Domain Controller）
> 2. **自动驾驶域控**（ADCU / ADAS Domain Controller）
>
> 法规侧对齐 **GB 44496-2024《汽车软件升级通用技术要求》**（2026-01-01 对新申报车型强制实施）与 **UN R156（SUMS）**。诊断侧对齐 **ISO 14229（UDS）**、**ISO 13400（DoIP）**、**ISO 15765-2（ISO-TP）**。文中参数为工程基线建议，量产以主机厂诊断规范与供应商刷写规范为准。

---

## 0. 文档目标与适用范围

### 0.1 目标

本框架用于规定：

| 编号 | 目标 |
| --- | --- |
| G1 | 明确云端、车载 OTA Master、Target ECU 的职责与接口 |
| G2 | 定义座舱 SoC / 智驾域控在车载刷写网络中的拓扑与传输路径 |
| G3 | 落实升级前置安全条件、包保护、验签与失败可恢复 |
| G4 | 固化 UDS 刷写时序、关键服务与 DID 读回要求 |
| G5 | 定义异常、重试、回滚与 OTA 状态机 |

### 0.2 范围与非范围

**范围内**

- 在线升级（OTA）触发的远程刷写链路
- Target = 座舱 SoC、智驾域控及其附属 MCU/安全岛（若由 Master 统一编排）
- 诊断会话下的编程下载（Programming Session）

**范围外（需另册）**

- 云端业务编排、灰度策略、用户触达文案细则
- 产线 EOL 刷写治具专用流程（可复用 UDS，但时序与电源策略不同）
- 功能安全 ASIL 分解与安全案例正文（本框架只给接口与门禁要求）

### 0.3 术语

| 术语 | 含义 |
| --- | --- |
| **OTA Backend** | 云端升级后端：任务编排、包分发、策略、审计 |
| **OTA Master** | 车载升级主控（常位于 HPC / 中央网关 / T-Box 协同节点） |
| **Target ECU** | 被刷写对象：座舱 SoC 或智驾域控 |
| **SWIN** | Software Identification Number，软件识别码 |
| **SUMS** | Software Update Management System，软件升级管理体系 |
| **A/B（Dual-Bank）** | 双分区：活动区运行，非活动区写入，校验通过后切换 |
| **Safe State Check** | 升级前置安全条件检查 |

---

## 1. 系统架构与角色划分

### 1.1 逻辑架构

<div class="mermaid">
flowchart TB
    subgraph Cloud["云端 OTA Backend"]
        CM["Campaign / Policy"]
        PKG["Package Repo<br/>加密包 + 签名 + 元数据"]
        AUD["Audit / Trace<br/>任务与结果留存"]
    end

    subgraph Vehicle["车端"]
        TB["T-Box / CCU<br/>蜂窝链路"]
        OM["OTA Master<br/>HPC / Central Gateway"]
        subgraph Targets["Target ECU"]
            CK["Cockpit SoC<br/>IVI / CDC"]
            AD["ADCU<br/>智驾域控"]
        end
    end

    CM --> PKG
    PKG --> TB
    TB --> OM
    OM -->|DoIP / UDS| CK
    OM -->|DoIP / UDS<br/>或 CAN/ISO-TP| AD
    OM --> AUD
    TB --> AUD

    style Cloud fill:#c7ceea,color:#3d3556
    style Vehicle fill:#bee3db,color:#3d3556
    style Targets fill:#ffdac1,color:#3d3556
</div>

### 1.2 职责边界（必须写进规格书）

#### 1.2.1 云端 OTA Backend

| 职责 | 说明 |
| --- | --- |
| 任务编排 | 目标车型/VIN 列表、依赖顺序、灰度、强制/可选策略 |
| 包管理 | 生成差分/全量包；绑定版本、SWIN、兼容矩阵、回滚基线 |
| 安全材料 | 加密密钥托管、签名证书链、吊销列表同步 |
| 合规评估入口 | 触发型式认证影响评估、风险评估记录（SUMS/GB 过程证据） |
| 下发与监控 | 下发任务；接收车端进度、结果码、日志摘要 |
| 审计留存 | 升级记录保存周期满足法规（GB 要求相关记录保存至车型停产后不少于规定年限） |

**云端不做**：直接向 Target 发 UDS；不越过 OTA Master 控制车内诊断会话。

#### 1.2.2 车载 OTA Master（HPC / 中央网关）

| 职责 | 说明 |
| --- | --- |
| 任务受理 | 鉴权云端任务；校验 VIN、车型、当前软件拓扑 |
| 包落盘 | 下载、断点续传、本地完整性校验、解密（若策略要求车端解密） |
| **Safe State Check** | 车速、档位、EPB、电源、用户确认等门禁 |
| 诊断会话主控 | 作为 Tester，对 Target 发起 UDS；维护 TesterPresent |
| 编排多目标 | 座舱 / 域控 / 依赖 MCU 的顺序、并行度、失败中止策略 |
| 状态机与上报 | 维护本车 OTA 状态；向云端与 HMI 上报 |
| 回滚决策 | 按策略触发分区回退或保底镜像恢复 |

**Master 不做**：替代 Target 内部 Bootloader 的签名验签最终裁决；不在未满足安全条件时进入 Programming Session。

#### 1.2.3 Target ECU（座舱 SoC / 智驾域控）

| 职责 | 说明 |
| --- | --- |
| 诊断服务端 | 实现 UDS Server（含 Bootloader / Updater 角色切换） |
| 安全访问 | 响应 0x27；配合 HSM/TEE 完成密钥运算 |
| 存储写入 | 按 0x34/0x36/0x37 接收并写入非活动区 |
| 完整性与验签 | 镜像 CRC/Hash + 数字签名验证 |
| 激活与启动 | 校验通过后切换启动槽位；失败保持旧槽位可启动 |
| 版本暴露 | 通过 DID（如 0xF195 等）回报软件标识 |

### 1.3 座舱 SoC vs 智驾域控：角色差异（规格书建议分表）

| 维度 | 智能座舱 SoC | 自动驾驶域控 |
| --- | --- | --- |
| 典型链路 | 车载以太网 + **DoIP** 为主 | DoIP 优先；部分子核/MCU 仍走 **CAN + ISO-TP** |
| 镜像形态 | 大镜像（Android/QNX + 应用分区），常差分 | 多分区（MCU + SoC + 模型/标定），常分通道刷写 |
| 运行约束 | 升级时可限制多媒体/部分应用；需防门锁死等 UX/安全要求 | 升级前通常要求 **智驾功能降级/退出**，禁止自动驾驶激活态刷写 |
| 电源 | 关注整车蓄电池 SOC + 座舱持续供电 | 关注域控独立供电/ redundent 电源与长时写入功耗 |
| 回滚 | A/B 系统分区 + 应用分区策略 | A/B + 安全岛保底镜像；模型/标定需版本绑定 |
| 失败影响 | 影响娱乐/显示/部分舒适功能 | 可能影响智驾使能；必须失败安全到「功能不可用但车辆可控」 |

---

## 2. 车载刷写网络架构拓扑

### 2.1 推荐拓扑（以太网骨干 + 诊断网关）

<div class="mermaid">
flowchart LR
    Cloud["OTA Cloud"] -->|HTTPS/MQTTS| TBOX["T-Box"]
    TBOX -->|ETH| MASTER["OTA Master<br/>HPC / GW"]
    MASTER -->|DoIP TCP 13400<br/>UDS| COCKPIT["Cockpit SoC"]
    MASTER -->|DoIP| ADCU["ADCU SoC"]
    MASTER -->|CAN FD<br/>ISO-TP UDS| ADMCU["ADCU Safety MCU"]
    MASTER -->|SOME/IP 等| HMI["HMI / 用户确认"]

    style Cloud fill:#c7ceea,color:#3d3556
    style MASTER fill:#b5ead7,color:#3d3556
    style COCKPIT fill:#ffdac1,color:#3d3556
    style ADCU fill:#ffdac1,color:#3d3556
    style ADMCU fill:#ffd6e0,color:#3d3556
</div>

### 2.2 传输层选型原则

| 路径 | 标准 | 适用 | 规格要求 |
| --- | --- | --- | --- |
| **UDS over DoIP** | ISO 13400 + ISO 14229 | 座舱 SoC、智驾主 SoC | 必须：Routing Activation、逻辑地址规划、TCP 保活、大块传输 |
| **UDS over CAN（ISO-TP）** | ISO 15765-2 + ISO 14229 | 域控安全 MCU、局部控制器 | 必须：流控、块大小、STmin、总线负载上限 |
| 包下载链路 | HTTPS 等 | T-Box → Master 本地存储 | 与诊断刷写分离；先完整落盘再开编程会话 |

### 2.3 地址与带宽（示例基线，可按项目改）

| 项目 | 建议写入规格 |
| --- | --- |
| DoIP 逻辑地址 | Master / Cockpit / ADCU 唯一逻辑地址表，禁止冲突 |
| 刷写窗口总线负载 | CAN FD 刷写时诊断以外应用报文策略（0x28 抑制范围） |
| 并发策略 | 默认**串行**刷写高算力节点；仅在无依赖且电源允许时并行 |
| TesterPresent | Programming Session 期间周期发送（如 2 s），超时退出会话 |

---

## 3. 安全与合规性设计（GB 44496-2024 / UN R156）

### 3.1 法规映射（规格书「合规」专章）

| 要求主题 | UN R156 | GB 44496-2024 | 车端落地要点 |
| --- | --- | --- | --- |
| 管理体系 | SUMS 认证 | 企业软件升级管理体系 | 过程、记录、供应商接口、内部审核 |
| 可追溯 | RXSWIN / 软件识别 | SWIN 或版本号管理 | DID/配置清单可机读上报 |
| 包保护 | 真实/完整 | 真实、完整、机密 | 加密传输 + 签名验签 |
| OTA 用户告知 | 告知目的/时长/影响 | 明确告知与确认 | HMI 弹窗 + 日志 |
| 电源保障 | 足够完成或安全恢复 | 电量门槛 | SOC/电源门禁 |
| 失败恢复 | 恢复旧版或进入安全状态 | 失败回滚等技术措施 | A/B 或保底镜像 |
| 安全执行 | 技术措施保障升级时安全 | 影响安全时的技术措施 | Safe State + 功能抑制 |
| 试验方法 | 原则性 | **给出试验方法** | 断电、篡改、失败注入用例入库 |

> 说明：GB 44496 在对标 R156 基础上强化了管理体系、车辆技术要求与试验方法；量产合规以标准正文与公告实施口径为准。

### 3.2 前置安全条件（Safe State Check）

升级进入 **Programming Session / 开始写 Flash** 前，OTA Master 必须全部满足；任一项失败则**禁止刷写**并上报原因码。

#### 3.2.1 整车级门禁（通用）

| 检查项 | 建议判据（示例） | 不满足时动作 |
| --- | --- | --- |
| 车速 | `VehicleSpeed == 0`（或 ≤ 项目阈值） | 拒绝；提示静止 |
| 档位 | P 档（自动）/ 空挡+驻车策略（按车型） | 拒绝 |
| EPB | 拉起 / Applied | 拒绝 |
| 充电/供电 | 蓄电池 SOC ≥ 阈值（如 40%+，按写时长标定）；或外接充电中 | 拒绝或仅允许下载不允许刷写 |
| 电源模式 | 允许的 Ignition/Ready 策略（项目定义） | 拒绝 |
| 用户确认 | 已确认告知内容（目的、时长、影响、失败后果） | 拒绝 |
| 防盗/锁车 | 满足「升级期间不得非预期锁止」等国标相关要求的技术策略 | 按策略阻断或改交互 |
| 诊断独占 | 无冲突外部 Tester；Master 获得总线诊断仲裁 | 拒绝 |

#### 3.2.2 座舱 SoC 附加门禁

| 检查项 | 说明 |
| --- | --- |
| 显示/通话关键任务 | 导航引导中、紧急通话中可禁止或延迟 |
| 存储空间 | 非活动槽位剩余空间 ≥ 包体 + 余量 |
| 温控 | SoC/存储温度在写入允许范围 |

#### 3.2.3 智驾域控附加门禁（更严）

| 检查项 | 说明 |
| --- | --- |
| 智驾状态 | **禁止**在 ACC/NOA/行车辅助激活时刷写 |
| 功能抑制 | 刷写前显式关闭智驾使能，HMI 提示「智驾暂不可用」 |
| 传感器标定依赖 | 若本次含标定/模型，检查是否要求静止与场地条件 |
| 安全岛健康 | Safety MCU / 监控芯片通信正常，允许进入升级模式 |

**规格书强制语句示例：**

> OTA Master 仅在 Safe State Check = PASS 且用户确认完成后，方可对 Target 发送 `DiagnosticSessionControl (0x10 02)`。

### 3.3 软件包加密、签名验签（PKI / HSM）

#### 3.3.1 包结构（逻辑）

```text
UpdatePackage
├── Manifest（版本、SWIN、依赖、分区、摘要、签名）
├── Payload（差分/全量；可分段）
├── Signature（OEM 根链下叶子证书签名）
└── Optional: Encryption Envelope（对称密钥密文）
```

#### 3.3.2 信任链

| 层级 | 职责 |
| --- | --- |
| OEM Root / Intermediate | 云端签发升级包签名证书 |
| Vehicle Trust Anchor | 车端只信任预置根/中间证书；支持吊销 |
| HSM / TEE / SE | 私钥不出安全单元；验签与解密在安全环境 |
| Anti-Rollback Counter | 单调版本计数，拒绝回退到已知漏洞版本（策略允许的「官方回滚包」除外） |

#### 3.3.3 验签时机（必须写清）

1. **下载完成**：Master 校验包完整性与签名（防错误包进入刷写）  
2. **写入完成**：Target Bootloader 再验签/验 Hash（防传输与落盘篡改）  
3. **激活前**：再次确认 Manifest 与槽位绑定关系  

任一失败：不激活新槽位，保持旧槽位可启动，上报 `IntegrityOrSignatureFailed`。

### 3.4 防刷写中断：Rollback / Dual-Bank

#### 3.4.1 推荐：A/B Dual-Bank

<div class="mermaid">
stateDiagram-v2
    [*] --> SlotA_Active: 出厂/当前运行
    SlotA_Active --> Writing_SlotB: 开始刷写非活动区
    Writing_SlotB --> Verify_SlotB: 传输结束
    Verify_SlotB --> SlotB_Active: 验签通过并切换
    Verify_SlotB --> SlotA_Active: 验签失败/中断
    SlotB_Active --> Writing_SlotA: 下一次升级
    Writing_SlotA --> SlotA_Active: 成功切换
    Writing_SlotA --> SlotB_Active: 失败保持
</div>

**硬性要求**

- 写入只针对**非活动槽位**
- 传输中断、断电：活动槽位仍可启动
- 切换是原子动作（Boot flag 单点提交）
- 切换失败自动落回旧槽位

#### 3.4.2 无法双分区时的保底策略

| 策略 | 适用 | 风险 |
| --- | --- | --- |
| Recovery Partition | 有独立恢复分区 | 需保证恢复分区自身只读/少更新 |
| 线刷救援 | 售后 | 不得作为 OTA 唯一恢复手段宣称 |

智驾域控若含「安全岛 + 主 SoC」，规格书应要求：**至少一处保底可启动镜像**，避免双端同时变砖。

---

## 4. UDS 刷写时序与诊断服务流程

### 4.1 总体阶段

| 阶段 | 目的 | 关键服务 |
| --- | --- | --- |
| Pre-check | 读版本、查前置条件 | 0x22, 0x31 |
| Session & Security | 进入编程并解锁 | 0x10, 0x27 |
| Bus Quiet | 抑制干扰与误报 | 0x28, 0x85 |
| Download | 擦除/下载/传数/结束 | 0x31, 0x34, 0x36, 0x37 |
| Post-check | 依赖与完整性 | 0x31, 0x22 |
| Finalize | 复位并恢复通信 | 0x11, 0x28, 0x85 |

### 4.2 Sequence Diagram：OTA Master ↔ Target ECU

<div class="mermaid">
sequenceDiagram
    autonumber
    participant M as OTA Master
    participant T as Target ECU<br/>Cockpit/ADCU

    Note over M: Safe State Check PASS<br/>Package verified locally

    M->>T: 0x10 03 ExtendedSession
    T-->>M: 0x50 03
    M->>T: 0x22 F195 (及项目规定 DID)
    T-->>M: 0x62 F195 + SW Version
    M->>T: 0x31 01 FF00 CheckProgrammingPreconditions
    T-->>M: 0x71 01 FF00 + result

    M->>T: 0x10 02 ProgrammingSession
    T-->>M: 0x50 02
    M->>T: 0x27 01 RequestSeed
    T-->>M: 0x67 01 + Seed
    M->>T: 0x27 02 SendKey
    T-->>M: 0x67 02

    M->>T: 0x85 02 DTCSettingOff
    T-->>M: 0xC5 02
    M->>T: 0x28 03 DisableRxAndTx (non-diag)
    T-->>M: 0x68 03

    opt Erase inactive bank
      M->>T: 0x31 01 FFxx EraseMemory
      T-->>M: 0x71 ... (含 0x78 pending)
    end

    M->>T: 0x34 RequestDownload
    T-->>M: 0x74 + maxBlockLength
    loop BlockSequenceCounter++
      M->>T: 0x36 TransferData
      T-->>M: 0x76
    end
    M->>T: 0x37 RequestTransferExit
    T-->>M: 0x77

    M->>T: 0x31 01 FFyy CheckMemory / VerifySignature
    T-->>M: 0x71 ... OK
    M->>T: 0x31 01 FFzz CheckProgrammingDependencies
    T-->>M: 0x71 ... OK

    M->>T: 0x11 01 ECUReset
    Note over T: Boot + Slot Switch
    M->>T: 0x10 03 ExtendedSession
    M->>T: 0x22 F195 读回新版本
    T-->>M: 0x62 F195 新版本
    M->>T: 0x85 01 DTCSettingOn
    M->>T: 0x28 00 EnableRxAndTx
</div>

> 实车常在多处出现 **NRC 0x78（Response Pending）**，Master 必须按 P2* 等待，禁止盲目重发导致状态错乱。

### 4.3 UDS 服务清单（本框架强制覆盖）

| SID | 服务名 | 刷写中的作用 | 典型子功能/备注 |
| --- | --- | --- | --- |
| **0x10** | DiagnosticSessionControl | 切扩展/编程会话 | `0x03` Extended；`0x02` Programming |
| **0x27** | SecurityAccess | 解锁刷写权限 | Seed/Key；建议绑定 HSM |
| **0x28** | CommunicationControl | 抑制非诊断通信 | 刷写前 Disable；结束后 Enable |
| **0x85** | ControlDTCSetting | 关闭/打开 DTC 记录 | 防半刷写窗口误报 |
| **0x34** | RequestDownload | 协商地址、长度、格式 | 指向非活动槽位 |
| **0x36** | TransferData | 分块传输 | BlockSequenceCounter 连续 |
| **0x37** | RequestTransferExit | 结束传输 | 可附带工具侧 CRC |
| **0x31** | RoutineControl | 前置检查/擦除/校验/依赖检查 | RID 项目化定义 |
| **0x11** | ECUReset | 复位以加载新软件 | HardReset 常见 |
| **0x22** | ReadDataByIdentifier | 读版本与状态 DID | 刷前刷后对比 |

**保活（建议写入）**：`0x3E TesterPresent`（可 suppressPosRsp），防止会话超时。

### 4.4 关键 DID（示例，项目需固化清单）

| DID | 常见含义 | 用途 |
| --- | --- | --- |
| **0xF195** | System Supplier ECU Software Number | 刷前基线、刷后验收 |
| 0xF189 | Vehicle Manufacturer ECU Software Number | OEM 软件号 |
| 0xF187 | Vehicle Manufacturer Spare Part Number | 零件号一致性 |
| 0xF18A | System Supplier Identifier | 供应商识别 |
| 0xF180 / 项目自定义 | Boot Software / Fingerprint | Boot 与指纹追溯 |
| 项目 DID | OTA Slot / Bank Status | 当前活动槽位、升级结果码 |
| 项目 DID | SWIN 列表 | 法规软件识别上报 |

**验收准则示例**：刷写成功的充要条件包括 `0x22 F195`（及项目规定集合）与任务 Manifest 目标版本一致，且槽位状态 = Active(New)。

### 4.5 NRC 与时序参数（规格书应附表）

| 项目 | 要求 |
| --- | --- |
| 常见 NRC | `0x12` subFunctionNotSupported，`0x13` incorrectMessageLength，`0x22` conditionsNotCorrect，`0x24` requestSequenceError，`0x31` requestOutOfRange，`0x33` securityAccessDenied，`0x35` invalidKey，`0x72` generalProgrammingFailure，`0x78` requestCorrectlyReceived-ResponsePending |
| P2 / P2* | 按诊断规范；Master 必须实现 0x78 等待状态机 |
| 块大小 | 以 0x74 返回的 maxNumberOfBlockLength 为准 |
| 会话超时 | S3Server；Master 用 0x3E 保活 |

---

## 5. 异常处理与状态机设计

### 5.1 车端 OTA 状态机

<div class="mermaid">
stateDiagram-v2
    [*] --> Inactive

    Inactive --> Downloading: 接收到合法任务且用户可稍后确认
    Downloading --> ReadyToFlash: 下载完成且验签通过
    Downloading --> Failure: 下载失败/校验失败超限
    ReadyToFlash --> Flashing: Safe State PASS + 用户确认
    ReadyToFlash --> Inactive: 任务取消/条件长期不满足
    Flashing --> Verifying: 传输结束
    Verifying --> Success: 版本读回一致且槽位切换成功
    Verifying --> RollingBack: 校验失败或激活失败
    RollingBack --> Success: 回滚到旧槽位成功（功能恢复）
    RollingBack --> Failure: 回滚失败（需售后救援策略）
    Flashing --> RollingBack: 超时/断电恢复后检测到不完整写入
    Success --> Inactive: 清理任务/等待下一任务
    Failure --> Inactive: 上报完成并释放资源

    note right of Flashing
      Programming Session 内
      禁止跳过 Safe State
    end note
</div>

#### 5.1.1 状态定义

| 状态 | 含义 | 允许的主要动作 |
| --- | --- | --- |
| **Inactive** | 无进行中任务 | 接收新任务 |
| **Downloading** | 包下载与落盘校验 | 断点续传；不可开编程写 Flash |
| **ReadyToFlash** | 包就绪，等待安全条件与确认 | 轮询 Safe State；展示 HMI |
| **Flashing** | UDS 编程传输中 | 仅诊断保活与传输；抑制无关功能 |
| **Verifying** | 校验、依赖检查、读版本 | 禁止再次写入同一槽位 |
| **RollingBack** | 激活失败后的回退 | 切回旧槽位/启动恢复 |
| **Success** | 目标版本生效 | 上报成功证据 |
| **Failure** | 不可自动恢复的失败 | 上报；引导售后或下次重试策略 |

### 5.2 异常场景矩阵

| 异常 | 检测 | 重试策略 | 回滚/恢复 | 上报 |
| --- | --- | --- | --- | --- |
| **通信超时** | P2/P2* 超时、TCP 断开、ISO-TP 流控失败 | 同一步有限重试（如 3 次）；超限中止会话 | 未切换槽位则保持旧版 | `CommTimeout` + 断点信息 |
| **0x78 过久** | Pending 超过项目上限 | 继续等待至上限；禁止并行插入其他写服务 | 中止并不激活 | `PendingTimeout` |
| **安全访问失败** | NRC 0x35/0x33 | 延迟后有限次；防暴力破解锁定 | 不进入下载 | `SecurityDenied` |
| **条件不满足** | NRC 0x22；Safe State 失败 | 退回 ReadyToFlash 等待 | 无写入则无需回滚 | `ConditionsNotCorrect` |
| **校验/验签失败** | 0x31 失败或本地验签失败 | **不重试激活**；可重新下载同包有限次 | 保持旧槽位 | `IntegrityFailed` |
| **块序号错误** | 0x36 序号不连续 | 从 RequestDownload 重建（按 Bootloader 能力） | 擦除不完整非活动区 | `TransferSequenceError` |
| **断电/复位** | 上电读槽位与进度指纹 | 若非活动区不完整：标记失败并清理或重刷该槽 | 活动槽启动；禁止启动半新半旧 | `PowerInterruption` |
| **复位后版本不符** | 0x22 读回 ≠ 目标 | 触发 RollingBack | 强制旧槽位 | `VersionMismatch` |
| **依赖检查失败** | CheckProgrammingDependencies | 不激活；可能需按编排回滚已刷依赖件 | 按任务原子性策略 | `DependencyFailed` |

### 5.3 重试与幂等原则

1. **下载可重试，激活不可侥幸重试**：验签失败不得「再 reset 碰运气」。  
2. **Flashing 步进可恢复**：以「已成功块序号 / 分区擦除标记」做断点，Bootloader 需定义是否支持 resume。  
3. **任务幂等**：同一 `CampaignId + Target + ToVersion` 重复下发，Master 应识别并避免双写。  
4. **云端重试不等于车端强刷**：云端可重派任务，车端仍必须重新走 Safe State。

### 5.4 断电专项（座舱 / 域控都要写）

| 阶段断电 | 期望行为 |
| --- | --- |
| Downloading | 续传；不进入编程 |
| Flashing 写非活动区 | 旧系统可启动；非活动区标记 Dirty |
| Verifying 前 | 同 Flashing |
| 槽位切换瞬间 | Bootloader 保证 flag 单点提交；最坏回到旧槽 |
| Success 后 | 正常 |

**测试要求（对齐 GB 试验思路）**：规范应引用「升级过程异常断电」「包篡改」「升级失败」等试验用例，作为入门/回归必测项。

---

## 6. 规格书建议目录（可直接粘贴）

```text
1 范围
2 规范性引用文件（ISO 14229 / 13400 / 15765、GB 44496、UN R156、OEM 诊断规范）
3 术语与缩略语
4 系统架构与职责
  4.1 云端 OTA Backend
  4.2 OTA Master
  4.3 Target ECU（座舱 SoC / 智驾域控分册）
5 网络拓扑与地址规划
6 安全与合规
  6.1 Safe State Check
  6.2 包加密与 PKI/HSM 验签
  6.3 Dual-Bank / Rollback
  6.4 用户告知与确认
7 UDS 刷写流程
  7.1 时序图
  7.2 服务与 RID/DID 清单
  7.3 NRC 与定时参数
8 状态机与异常处理
9 日志、审计与上报数据项
10 试验与验收（含断电/篡改/失败回滚）
11 开放问题与项目参数表
```

---

## 7. 验收检查清单（摘要）

| # | 检查项 | 通过准则 |
| --- | --- | --- |
| 1 | 职责边界 | 云端不直连 Target UDS；Master 为唯一车内 Tester |
| 2 | Safe State | 车速/档位/EPB/电源/用户确认/智驾退出均有机检 |
| 3 | 包安全 | 下载后与激活前双重完整性/签名校验 |
| 4 | 双分区 | 中断后旧槽可启动；无「半新系统」可被选为默认启动 |
| 5 | UDS 时序 | 0x10/27/28/85/34/36/37/31/11/22 路径可复现 |
| 6 | DID 读回 | 0xF195（及项目 DID）刷后与目标一致 |
| 7 | 状态机 | Inactive→…→Success/Failure 转换无死锁 |
| 8 | 断电试验 | 各阶段断电后恢复符合矩阵 |
| 9 | 合规证据 | SWIN/版本、任务日志、用户确认记录可导出审计 |

---

## 8. 结语

座舱 SoC 与智驾域控的 OTA，难在「大包 + 长窗口 + 高安全后果」。规格书若只写「支持 UDS 刷写」远远不够；必须把 **职责边界、安全门禁、信任链、双分区、UDS 时序、状态机与异常矩阵** 写成可测试条款。

对这两类节点，记住三条工程底线：

1. **先 Safe State，后 Programming Session**  
2. **只写非活动槽，验签通过才切换**  
3. **失败可解释、可回滚、可审计**——这既是 UN R156 / GB 44496 的要求，也是量产车上少变砖的方法

> 下一步可将本文第 6 章目录打成正式 Word/ReqIF，把 RID、DID、NRC、SOC 阈值、P2 定时等全部参数化进「项目参数表」，即可成为一版可评审的刷写技术规格书。

---

*框架依据 ISO 14229 UDS 常规刷写实践，并结合 GB 44496-2024、UN R156 的公开合规要点整理。具体子功能码、RID、DID 与安全算法以主机厂诊断规范及供应商 Bootloader 规格为准。*
