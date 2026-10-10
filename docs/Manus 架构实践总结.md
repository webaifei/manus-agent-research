# Manus 架构实践总结

> 基于本仓库 `docs/` 下的三份材料：
> ① [LangChain × Manus webinar 中英对照转写](Context%20Engineering%20for%20AI%20Agents%20with%20LangChain%20and%20Manus（中英文对照）.md)（主讲：季逸超，约 1 小时）
> ② [完整参考资料清单](Manus%20Agent%20架构%20·%20完整参考资料清单.md)（82 条链接，带 A/B/C/D 可信度分级）
> ③ [Manus 官博《Context Engineering》全文整理](manus-context-engineering.md)
>
> 另核对了 Manus 官方博客 1.5 / 2.0 / Wide Research 三篇，以及 E2B、PingCAP、Salt Labs 的公开材料。
> 每条结论后面标了出处。**转写里没说的，我不补。**

---

## 0. 一句话

Manus 的护城河不是模型，是**把模型放进一台属于用户的云 VM**，然后围绕一条硬约束——**KV cache 不能被打断**——把上下文管理做到了别家没做的程度。

季逸超自己给这条约束的量化是：缓存命中和不命中的差价可以到 **100:1**（官博《Context Engineering》，本仓库 `manus-context-engineering.md`）。

这句话解释了这个系统里几乎所有看起来奇怪的设计。

---

## 1. 现状架构（Manus 2.0，2026-09-28）

官方对 2.0 的定性是「不是版本更新，是一个新架构」（[Introducing Manus 2.0](https://manus.im/blog/introducing-manus-2-0)）。从公开材料能拼出六层：

```
① 模型层        不训练模型；路由多个前沿模型；按任务分层（Manus-1.5 / 1.5-Lite）
                    ↓
② Harness 层    Cascade —— 自研 agent harness，按需引入专用能力
                    ↓
③ 动作空间      三层渐进：function calling / sandbox utilities / packages+APIs
                    ↓
④ 执行层        每个 session 一台专用云 VM（Firecracker microVM）
                    ↓
⑤ 记忆层        文件系统当记忆 + 压缩 + 外部数据库（TiDB 存长期记忆）
                    ↓
⑥ 协作层        Wide Research（几百个同构 agent 并行）+ Connectors + Automations
```

逐层说明：

**① 模型层：不训模型，而且明确不用开源模型。**
理由不是质量，是**成本**。转写里说得很直接（行 1321）：agent 的输入远长于输出、KV cache 是关键，而分布式 KV cache 在开源方案上难做，算下来旗舰模型有时反而更便宜。

**② Harness 层：Cascade。**
官方描述是「keeps a project light from the start and brings in specialized capabilities only when the work needs them」。实测一组配置：**token 少 23.2%、时间少 28.2%、成本低 32%**（Manus 2.0 官博）。

**③ 动作空间：三层，而不是一张扁平的工具表。**
这是被最多人误解的地方——见第 2 节第 3 条。

**④ 执行层：一 session 一 VM。**
官方原话是「the Turing-completeness of the virtual machine is what gives Manus its generality」（[Wide Research 官博](https://manus.im/en/blog/introducing-wide-research)）。底层 2025 年 5 月起用 E2B 的 Firecracker microVM，启动约 150ms，**明确弃用了 Docker**（[E2B 客户案例](https://e2b.dev/customers/how-manus-uses-e2b-to-provide-agents-with-virtual-computers)）。

**⑤ 记忆层：文件系统当记忆 + 可逆压缩。**
不用 RAG、不用向量库当主记忆。压缩的规则非常具体，见第 2 节第 8 条。

**⑥ 协作层：同构 agent，不是角色分工。**
见第 2 节第 7 条。

---

## 2. 十条与「常规做法」相反的取舍

这一节是全文的重点。前八条来自 webinar 转写（带行号），后两条来自官博与第三方研究。

### 1）不按角色划分多智能体 —— 没有 designer / manager / programming agent

**常规做法**：给每个 agent 起个职位名，manager 调度 worker。
**Manus 的做法**：只有 general executor、planner、knowledge management（以及 data API registration）几种，**都是功能性的，不是职位性的**。
**理由**（转写 行 1577）：按角色划分是照搬人类公司的组织架构，而那种架构本身来自**人类的上下文限制**——模型没有这个限制，抄它没有意义。

### 2）弃用了 todo.md —— 也就是官方自己那篇博客里的标志性设计

**常规做法**：把 todo.md 当外部记忆，让模型反复重写。
**现状**：**已经不用了**。改成用 agent-as-tool 实现的独立 planner agent。
**理由**（转写 行 1513）：3–4 月的日志里，大约 **1/3 的动作**都在更新 todo 列表——纯 token 浪费，换成了「按需调用一个 planner agent」。

> 这条值得单拎出来：**官方推翻了自己最有名的那篇博客里的做法。** 你在网上读到的大部分 Manus 解析都还停在 todo.md 那一版。

### 3）拒绝「工具描述动态 RAG」，改用三层动作空间

**常规做法**：工具太多就把工具描述做 RAG，按需检索注入。
**Manus 的做法**：三层渐进——① function calling 空间（少量、静态）② sandbox utilities ③ packages / APIs（在沙箱里现装现用）。
**两条否定理由都很硬**（转写 行 713–729）：
- 工具定义在 prompt 前部，动态变一次 → **KV cache 每次重置**；
- 被移除工具的**旧调用记录还留在上下文里**，会 few-shot 诱导模型去调用一个已经不存在的工具。

### 4）MCP 工具不注入 function calling 空间

**做法**：MCP 工具全部通过沙箱里的 `Manus MCP CLI` 走命令行调用，**不进模型的 function calling 列表**。
**理由**：保住那个「紧凑的静态动作空间」。转写里说 MCP 的发布「完全改变了 Manus 的设计，从紧凑静态动作空间变成近乎无限可扩展」（行 489 / 行 777）。

### 5）试过「完全 CodeAct」，然后放弃了

**流行叙事**：代码智能体（让模型写代码调工具）是终局。
**实际**（转写 行 1433 / 行 1465）：试过完全 CodeAct，**放弃了**——因为代码没法做 constrained decoding。最终是混合模式。

### 6）压缩：只压最旧的那 50% 工具调用

**常规做法**：一压全压。
**Manus 的做法**：只压**最旧的 50% 工具调用**，较新的保留全量；总结永远用完整数据，且保留最后几个工具调用原文。
**理由**（转写 行 585 / 601）：否则模型会**模仿 compact 后的格式**去输出——字段缺失的工具调用。保留原文是为了防止模型在总结后改变风格与语气。

### 7）腐化阈值是个具体数字：128k–200k

转写 行 569 给了可操作阈值：模型硬上限 1M，但**大约 200k 起开始退化**，所以用 128k–200k 作为上下文缩减的触发点。

### 8）保留错误，而不是重试或清理

**常规做法**：失败就重试、把错误从上下文里抹掉。
**Manus 的做法**：把失败留在上下文里，让模型看到自己错在哪。
**理由**：这是官博里那条「保留错误」的原始意图——**擦掉错误，模型就会重复它**。

### 9）最大的一次提升来自「删东西」

转写 行 857 / 841 的收尾很反直觉：最大的飞跃来自**删掉技巧，而不是加更多上下文管理层**。原话大意是 "build less and understand more"，并明确警告 **context over-engineering**。

### 10）明确不做 RL

**理由**（转写 行 1769 / 1785）：MCP 让动作空间不再是固定的，reward 难设计、rollout 不平衡，做 RL 等于自己从头训基础模型。转向无参数（parameter-free）的在线学习和集体反馈。

---

## 3. 迭代过程

从公开时间线看，**2025 年 3 月上线到 10 月，整体重构了 5 次**（转写 行 1065）。

| 时间 | 事件 | 架构上的意义 |
|---|---|---|
| 2025-03 | 上线；prompt 泄露（29 工具全 schema + planner / knowledge / datasource 三模块） | 起点：单 VM + 扁平工具表 |
| 2025-05 | E2B 案例公开 | Firecracker microVM、150ms 启动、**弃用 Docker** |
| 2025-07-18 | 官博《Context Engineering》 | 公开 KV-cache、100:1、遮蔽工具、文件系统当记忆、保留错误 |
| 2025-07-31 | Wide Research | 几百个**同构** agent 并行，各自独立 context |
| 2025-10-16 | Manus 1.5 | **重架构引擎**；任务完成 15min → **<4min**；质量 +15%、满意度 +6%；单任务上下文窗口扩大 |
| 2025-12 | AWS 案例 | 上 Bedrock；韩媒转述「3 人管数万沙盒」 |
| 2026-03-16 | My Computer | 从云端 VM 延伸到本地桌面 agent |
| 2026-09-28 | **Manus 2.0** | **Cascade** 新 harness（token −23.2%、时间 −28.2%、成本 −32%）；Cloud Computer；Automations；Cue |

**迭代方法论本身也值得记**（转写 行 1097 / 1113）：

> 固定 agent 架构不动，换不同的模型去跑。如果**弱模型换强模型能带来明显增益**，说明架构还有前瞻性；如果没有增益，说明瓶颈在架构。
> 每 **1–2 个月**复审一次。

用「模型代际增益」当架构健康度的探针——这比追静态 benchmark 更能反映真实设计质量。

**评测体系也换过**（转写 行 1737）：上线后发现 GAIA 这类公共基准**与用户喜好严重不对齐**，于是换成三类：用户 1–5 星评分、内部可验证的自动化任务（含自建的执行型/事务型任务）、以及**人类实习生**评「品味」类任务。

---

## 4. 代价：VM 隔离解决了通用性，也放大了沙箱内的攻击面

两条第三方安全研究值得放在架构总结里读，因为它们直接指向上面那些设计选择的副作用：

- **Salt Labs（2026-10-01）**：一封邮件就能劫持 agent；用 JSFuck 绕过检测；最关键的一条是**护栏的检测发生得比执行晚**，而凭证就在沙箱里（[原文](https://salt.security/blog/how-we-hijacked-an-ai-agent-with-a-single-email)）。
- **Aurascape AuraLabs「SilentBridge」（2026-02-24）**：CVSS 9.8；**免密 sudo 拿到 root**；滥用 `deploy_expose_port`；CDN 跨租户（[原文](https://aurascape.ai/resources/auralabs-research/silentbridge-zero-click-agent-takeover-meta-manus/)）。

把这两条和第 1 节的执行层放在一起看，逻辑很清楚：**「一 session 一 VM」买到的是通用性，付出的代价是每个 VM 内部都是一个完整的、可横向移动的环境。** 通用 agent 的能力上限和攻击面是同一件事的两面。

---

## 5. 哪些能当事实用，哪些不能

摘自参考资料清单的分级，这里只留最容易踩坑的：

| 材料 | 能用吗 |
|---|---|
| 官博、官方文档、Manus API（含 OpenAPI spec、task lifecycle 事件词表） | **A**，可当事实引用 |
| PingCAP 案例研究（工程 VP 主讲） | **A**：Action Engine = Plan → Execute → Verify；最多 15,000 次迭代；TiDB 当长期记忆 |
| Salt Labs / AuraLabs 安全研究 | **A**，架构价值最高 |
| 泄露的 prompt（`manus_20250310.md`） | **B**：29 工具 schema 可信；V4 那份的 recitation 部分**字面不可引** |
| 第三方工程分析（philschmid、ZenML） | **B/C**，有整理错误（ZenML 的人名/日期就是错的） |
| 「五层架构」那篇腾讯云开发者社区文章 | **D**：AI 生成的假说，不要引 |
| arXiv:2505.02024 | **D**：编造 RLHF，不要引 |
| 虎嗅/搜狐那版译文 | **D**：把 token 单价误译成「0.30 美元/千标记」，原文是 **$0.30/百万 token** |

**Webinar 转写本身的存疑处**（详见 [`_extract-webinar.md`](_extract-webinar.md) 末章）：文件头标 1h52s，但最后时间戳只到 01:00:43，**转录可能只覆盖约一小时**；「Wide Agents」疑为 Wide Research 误听；说话人自称的「30 个工具」是随口举的随机数，**不能当规格**；全文**没有出现**任何价格、cache 命中率、并发数或延迟数值。

---

## 6. 和「传统 agent 最佳实践」的总对照

| 维度 | 常见做法 | Manus | 分歧点 |
|---|---|---|---|
| 上下文 | 动态增删系统提示 / 工具 | **append-only，前缀稳定** | KV cache 命中率 ≈ 成本 |
| 工具过多 | 工具描述 RAG | **三层分层动作空间** | 动态会打断缓存 + 旧调用残留 |
| MCP | 注入 function calling | **沙箱内 CLI 调用** | 保住紧凑静态动作空间 |
| 记忆 | RAG / 向量库 | **文件系统 + 可逆压缩** | 可写、可恢复、不丢中间产物 |
| 压缩 | 全量摘要 | **只压最旧 50% 的工具调用** | 压缩方式会被模型模仿 |
| 出错 | 重试 / 清理 | **保留在上下文里** | 抹掉错误 = 重复错误 |
| 分工 | 按角色分多 agent | **同构 agent + 独立 context** | 角色分工源自人类上下文限制 |
| 规划 | todo.md 外部记忆 | **agent-as-tool 的 planner** | 官方已弃用 todo.md |
| 工具调用 | function calling 或纯代码 | **混合** | 纯代码无法 constrained decoding |
| 训练 | RL 微调 | **不做 RL**，无参数在线学习 | MCP 让动作空间不固定 |
| 评测 | 公共 benchmark | **用户评分 + 内部自动化 + 人类评品味** | GAIA 与用户喜好不对齐 |
| 执行 | 共享容器 | **一 session 一 microVM** | 通用性的代价是攻击面 |

一句话读法：**别的系统在「怎么让模型更聪明」上加东西，Manus 在「怎么让模型的缓存不失效」上删东西。**

---

## 附：本仓库文件

```
docs/
├── Context Engineering for AI Agents with LangChain and Manus（中英文对照）.md   # webinar 转写
├── Manus Agent 架构 · 完整参考资料清单.md                                        # 82 条链接 + 分级
├── manus-context-engineering.md                                                # 官博全文整理
├── Manus 架构实践总结.md                                                        # ← 本文
└── _extract-webinar.md                                                         # 转写的结构化提取（含行号锚点）
```
