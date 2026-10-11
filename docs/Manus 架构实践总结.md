# Manus 架构实践总结

> 基于本仓库 `docs/` 下的三份材料：
> ① [LangChain × Manus webinar 中英对照转写](Context%20Engineering%20for%20AI%20Agents%20with%20LangChain%20and%20Manus（中英文对照）.md)（主讲：季逸超，约 1 小时）
> ② [完整参考资料清单](Manus%20Agent%20架构%20·%20完整参考资料清单.md)（82 条链接，带 A/B/C/D 可信度分级）
> ③ [Manus 官博《Context Engineering》全文整理](manus-context-engineering.md)
>
> 第二批另读了四份高可信材料，要点见 [补充材料笔记](补充材料笔记.md)：
> 极客公园 Stripe Tour 对谈全文（2025-08）、E2B 客户案例（2025-05）、**AWS 新闻稿（2025-12）**、philschmid《Context Engineering Part 2》（2025-12）。
>
> 每条结论后面标了出处。**转写里没说的，我不补。**

---

## 0. 一句话

Manus 的护城河不是模型，是**把模型放进一台属于用户的云 VM**，然后围绕一条硬约束——**KV cache 不能被打断**——把上下文管理做到了别家没做的程度。

季逸超自己给这条约束的量化是：缓存命中和不命中的差价可以到 **100:1**（官博《Context Engineering》，本仓库 `manus-context-engineering.md`）。

这句话解释了这个系统里几乎所有看起来奇怪的设计。

---

## 1. 现状架构（Manus 2.0，2026-09-28）

官方对 2.0 的定性是「不是版本更新，是一个新架构」（[Introducing Manus 2.0](https://manus.im/blog/introducing-manus-2-0)）。从公开材料能拼出六层，外加一条定义：

```
⓪ 定义层        Agent = 用户 + 模型 + 环境    ← 环境是定义的一部分，不是实现细节
                    ↓
① 模型层        不训练模型；Amazon Bedrock 统一治理；按任务分层（Manus-1.5 / 1.5-Lite）
                    ↓
② Harness 层    Cascade —— 自研 agent harness，按需引入专用能力
                    ↓
③ 动作空间      三层渐进：function calling / sandbox utilities / packages+APIs
                    ↓
④ 执行层        每个 session 一台专用云 VM（Firecracker microVM + E2B 集群调度）
                    ↓
⑤ 记忆层        文件系统当记忆 + 压缩 + 外部数据库（TiDB 存长期记忆）
                    ↓
⑥ 协作层        Wide Research（几百个同构 agent 并行）+ Connectors + Automations
```

逐层说明：

**⓪ 定义层：Agent 是三段，不是两段。**
季逸超在 [极客公园对谈](https://w.geekpark.net/news/352981) 里给的定义：聊天机器人和生成式工具「通常只有两个角色：用户和模型」，而 Agent「除了用户和模型，还引入了第三个关键元素——**环境**」；「在 Manus 这里，我们的目标是让 Agent 出现在**虚拟机甚至整个互联网**中」。

这一条是理解后面所有设计的前提：**做云 VM 不是工程选择，是定义上的必要条件。** 没有环境，按他的标准就不算 Agent。

**① 模型层：不训模型，而且明确不用开源模型。**
理由不是质量，是**成本**。转写里说得很直接（行 1321）：agent 的输入远长于输出、KV cache 是关键，而分布式 KV cache 在开源方案上难做，算下来旗舰模型有时反而更便宜。模型选择走 **Amazon Bedrock** 做统一治理，按场景挑（自然语言理解、任务分解、方案规划、代码生成与调试）（[AWS 新闻稿](https://press.aboutamazon.com/aws/2025/12/manus-selects-aws-to-power-general-purpose-ai-agent-serving-millions-globally)）。

**② Harness 层：Cascade。**
官方描述是「keeps a project light from the start and brings in specialized capabilities only when the work needs them」。实测一组配置：**token 少 23.2%、时间少 28.2%、成本低 32%**（Manus 2.0 官博）。

**③ 动作空间：三层，而不是一张扁平的工具表。**
这是被最多人误解的地方——见第 2 节第 3 条。

**④ 执行层：一 session 一 VM。**
官方原话是「the Turing-completeness of the virtual machine is what gives Manus its generality」（[Wide Research 官博](https://manus.im/en/blog/introducing-wide-research)）。底层是 **Firecracker microVM + E2B 集群调度**，2025 年 5 月起用，**明确弃用了 Docker**。

AWS 新闻稿给了这一层的硬指标：

| 指标 | 数字 |
|---|---|
| 沙箱实例启动 | 约 **125 毫秒** |
| 单沙箱内存 | 约 **5 MiB** |
| 单台服务器并发实例 | **数千个** |
| 运维人员规模 | **3 人管理数万个沙箱实例** |
| 开发与运维人力需求 | 下降约 **60%** |
| 整体运营效率 | 提升 **70%** |
| 计算成本 | 下降 **68%** |

其余技术栈：**Amazon Aurora Serverless + EKS + AWS Glue + Amazon MSK (Kafka)**，应对流量波动。E2B 是**自托管**在 Manus 自己的机器上（[E2B 案例](https://e2b.dev/customers/how-manus-uses-e2b-to-provide-agents-with-virtual-computers)）。

**为什么是 microVM 而不是容器**——E2B 那篇里张涛给的理由比「太慢」更准确：Docker 启动要 10–20 秒只是次要问题，**主要问题是容器没有完整 OS 功能**，装不了 app 和 Python 包——而「在沙箱里现装现用」正是第 3 层动作空间的前提。

**⑤ 记忆层：文件系统当记忆 + 可逆压缩。**
不用 RAG、不用向量库当主记忆。压缩的规则非常具体，见第 2 节第 6 条。

**⑥ 协作层：同构 agent，不是角色分工。**
见第 2 节第 1 条。

---

## 2. 十二条与「常规做法」相反的取舍

这一节是全文的重点。大部分出自 webinar 转写（带 `行 NNN` 行号），另标注了官博、极客公园对谈、E2B 案例与 philschmid 整理的来源。

先统一几个术语（[philschmid](https://www.philschmid.de/context-engineering-part-2)，也是这套实践的通用命名）：

| 术语 | 含义 |
|---|---|
| **Context Engineering** | 四件事：Offloading（移到外部系统）、Reduction（压缩历史）、Retrieval（动态加信息）、**Isolation（隔离上下文）** |
| **Agent Harness** | 包住模型的那层软件：执行工具调用、管理消息历史循环、处理上下文工程逻辑 |
| **Context Rot** | 上下文填满时性能退化，**即使远未到达技术上限**——「有效上下文窗口常远小于宣称值」 |
| **Context Pollution** | 无关、冗余、冲突的信息干扰推理 |
| **Context Confusion** | 模型分不清指令 / 数据 / 结构标记，或遇到逻辑上互斥的指令 |

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
**Manus 的做法**：三层渐进（[philschmid 整理](https://www.philschmid.de/context-engineering-part-2)给出了各层的确切定义）：

| 层 | 内容 |
|---|---|
| Level 1（Atomic） | 模型看到约 **20 个核心工具**（`file_write` / `browser_navigate` / `bash` / `search`…），稳定、cache 友好 |
| Level 2（Sandbox Utilities） | 不给每个工具单独定义，而是在 `bash` 里通过 CLI 调用。Manus 有 `mcp-cli <command>`，**把工具定义留在上下文窗口之外** |
| Level 3（Code/Packages） | 复杂逻辑链不来回问模型，给库让 agent 写脚本一次跑完 |

**两条否定理由都很硬**（转写 行 713–729）：
- 工具定义在 prompt 前部，动态变一次 → **KV cache 每次重置**；
- 被移除工具的**旧调用记录还留在上下文里**，会 few-shot 诱导模型去调用一个已经不存在的工具。

### 4）MCP 工具不注入 function calling 空间

**做法**：MCP 工具全部通过沙箱里的 `Manus MCP CLI` 走命令行调用，**不进模型的 function calling 列表**。
**理由**：保住那个「紧凑的静态动作空间」。转写里说 MCP 的发布「完全改变了 Manus 的设计，从紧凑静态动作空间变成近乎无限可扩展」（行 489 / 行 777）。

### 5）试过「完全 CodeAct」，然后放弃了

**流行叙事**：代码智能体（让模型写代码调工具）是终局。
**实际**（转写 行 1433 / 行 1465）：试过完全 CodeAct，**放弃了**——因为代码没法做 constrained decoding。最终是混合模式。

### 6）压缩有优先级：raw > Compaction > Summarization

**常规做法**：一压全压，压完就丢。
**Manus 的做法**分两种，且**优先选可逆的那种**：

- **Compaction（可逆）**：剥掉「环境里已经存在」的冗余信息。写了 500 行代码，历史里**只留路径**（`Output saved to /src/main.py`），要读的时候用工具再读回来。
- **Summarization（有损）**：到阈值（如 128k）才用 LLM 总结，而且**只压最旧的 50% 工具调用**，较新的保留全量；总结永远用完整数据，且**保留最后几次工具调用的原格式全文**。

**理由**（转写 行 585 / 601 + philschmid）：否则模型会**模仿 compact 后的格式**去输出——字段缺失的工具调用。保留原文是为了维持模型的「节奏（rhythm）」和输出风格。

### 7）腐化阈值是个具体数字

转写 行 569：模型硬上限 1M，但**大约 200k 起开始退化**，所以用 **128k–200k** 作为上下文缩减的触发点。
philschmid 给的数字略有不同：**< 256k** 就该设「Pre-Rot Threshold」。两个口径（触发点 vs 退化起点）都列，不做归一。

### 8）保留错误，而不是重试或清理

**常规做法**：失败就重试、把错误从上下文里抹掉。
**Manus 的做法**：把失败留在上下文里，让模型看到自己错在哪。
**理由**：这是官博里那条「保留错误」的原始意图——**擦掉错误，模型就会重复它**。

### 9）最大的一次提升来自「删东西」

转写 行 857 / 841 的收尾很反直觉：最大的飞跃来自**删掉技巧，而不是加更多上下文管理层**。原话大意是 "build less and understand more"，并明确警告 **context over-engineering**。

philschmid 把它总结成一条可检验的判据：

> **If your harness is getting more complex while models improve, you are likely over-engineering.**

他列的对照是：Manus 六个月重写 **5 次**，LangChain 的 Open Deep Research 重构 **4 次**——「This is normal」。

### 10）明确不做 RL

**理由**（转写 行 1769 / 1785）：MCP 让动作空间不再是固定的，reward 难设计、rollout 不平衡，做 RL 等于自己从头训基础模型。转向无参数（parameter-free）的在线学习和集体反馈。

philschmid 从另一侧给了同一个结论：**不要训自己的模型**——「We are living the Bitter Lesson」，在特定动作空间上训 RL 等于「locking yourself into a local optimum」。

### 11）工具的网络效应：灵活性优于硬编码规则

官博没写这条，出自 [极客公园对谈](https://w.geekpark.net/news/352981)。

**例子**：图表里显示中文/韩文时字体出错。常规做法是写硬编码规则（「输出韩文时用哪种字体」）——季逸超说「这种方式会让系统越来越僵化」。
**Manus 的做法**：给系统加一个能力——**查看图像**。结果模型会在生成后自己检查、发现错误、自动修正。
**结论**：「**增加工具的灵活性比硬编码规则能解决更多的问题。**」

配套的是「一次会话、一个共享上下文」：做研究再生成网站，难点不是搭网站，是**数据可靠性**，所以整条链要在一个上下文里完成。

### 12）沙箱会被暂停，而不只是被执行

这是环境交互的真实边界面，出自 E2B 案例：agent 需要在三种情况下**暂停会话、把控制权交回人**——
① 要和用户确认；② 需要网站凭证；③ **要过 CAPTCHA（"verify you are human"）**。

季逸超在极客公园谈到的瓶颈与之呼应：「网页工具就像小游戏一样，没有提供 API」，「验证码 CAPTCHA 无处不在，处处在拦截 Agent」——「这不是技术问题，而是更像是一种**制度性限制**」。

---

## 3. 迭代过程

> 产品 / 商业线与技术架构线的完整拆解，见 [Manus 迭代时间线 · 产品与技术](Manus%20迭代时间线%20·%20产品与技术.md)。

从公开时间线看，**2025 年 3 月上线到 10 月，整体重构了 5 次**（转写 行 1065）。

| 时间 | 事件 | 架构上的意义 |
|---|---|---|
| 2025-03 | 上线；prompt 泄露（29 工具全 schema + planner / knowledge / datasource 三模块） | 起点：单 VM + 扁平工具表 |
| 2025-05 | E2B 案例公开 | Firecracker microVM、150ms 启动、**弃用 Docker** |
| 2025-07-18 | 官博《Context Engineering》 | 公开 KV-cache、100:1、遮蔽工具、文件系统当记忆、保留错误 |
| 2025-07-31 | Wide Research | 几百个**同构** agent 并行，各自独立 context |
| 2025-10-16 | Manus 1.5 | **重架构引擎**；任务完成 15min → **<4min**；质量 +15%、满意度 +6%；单任务上下文窗口扩大 |
| 2025-12-03 | **AWS 新闻稿** | 技术栈成型：Bedrock + Firecracker/E2B + Aurora Serverless/EKS/Glue/MSK；**3 人管数万沙箱、125ms、5MiB、计算成本 −68%** |
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
| **AWS 新闻稿**（本批新读） | **A**：沙箱 125ms / 5MiB / 3 人管数万实例 / 成本 −68% 等硬数字的唯一出处 |
| **极客公园 Stripe Tour 对谈全文**（本批新读） | **A**（媒体整理的一手对谈）：Agent 三段定义、工具的网络效应、AGI 观 |
| PingCAP 案例研究（工程 VP 主讲） | **A**：Action Engine = Plan → Execute → Verify；最多 15,000 次迭代；TiDB 当长期记忆 |
| Salt Labs / AuraLabs 安全研究 | **A**，架构价值最高 |
| **E2B 客户案例**（本批新读） | **B**：供应商写的，但直接引联合创始人原话；planner→executor、27 工具、弃用 Docker 的真实原因 |
| **philschmid Context Engineering Part 2**（本批新读） | **B**：对 webinar 最好的英文整理；术语与三层动作空间的定义出自这里 |
| 泄露的 prompt（`manus_20250310.md`） | **B**：29 工具 schema 可信；V4 那份的 recitation 部分**字面不可引** |
| 第三方工程分析（ZenML 等） | **C**，有整理错误（ZenML 的人名/日期就是错的） |
| 「五层架构」那篇腾讯云开发者社区文章 | **D**：AI 生成的假说，不要引 |
| arXiv:2505.02024 | **D**：编造 RLHF，不要引 |
| 虎嗅/搜狐那版译文 | **D**：把 token 单价误译成「0.30 美元/千标记」，原文是 **$0.30/百万 token** |

**Webinar 转写本身的存疑处**（详见 [`_extract-webinar.md`](_extract-webinar.md) 末章）：文件头标 1h52s，但最后时间戳只到 01:00:43，**转录可能只覆盖约一小时**；「Wide Agents」疑为 Wide Research 误听；说话人自称的「30 个工具」是随口举的随机数，**不能当规格**；全文**没有出现**任何价格、cache 命中率、并发数或延迟数值——**本批的 AWS 新闻稿补上了并发、延迟和成本，但 cache 命中率仍然没有。**

**本批材料的交叉印证与冲突**，另见 [`补充材料笔记.md`](补充材料笔记.md) 第五节。要点：工具数（E2B 说 27，与泄露的 V4 一致）、重写次数（5 次，两处独立印证）、todo.md 浪费（1/3 动作 vs ~30% token）；冲突项是腐化阈值（128k–200k vs <256k）与沙箱延迟（150ms vs 125ms，后者较新）。

---

## 6. 和「传统 agent 最佳实践」的总对照

| 维度 | 常见做法 | Manus | 分歧点 |
|---|---|---|---|
| 定义 | Agent = 用户 + 模型 | **+ 环境**（VM 甚至整个互联网） | 环境是定义的一部分，不是实现细节 |
| 上下文 | 动态增删系统提示 / 工具 | **append-only，前缀稳定** | KV cache 命中率 ≈ 成本 |
| 工具过多 | 工具描述 RAG | **三层分层动作空间** | 动态会打断缓存 + 旧调用残留 |
| MCP | 注入 function calling | **沙箱内 CLI 调用** | 保住紧凑静态动作空间 |
| 记忆 | RAG / 向量库 | **文件系统 + 可逆压缩** | 可写、可恢复、不丢中间产物 |
| 压缩 | 全量摘要 | **raw > Compaction > Summarization** | 压缩方式会被模型模仿 |
| 出错 | 重试 / 清理 | **保留在上下文里** | 抹掉错误 = 重复错误 |
| 分工 | 按角色分多 agent | **同构 agent + 独立 context** | 角色分工源自人类上下文限制 |
| 规划 | todo.md 外部记忆 | **agent-as-tool 的 planner** | 官方已弃用 todo.md |
| 工具调用 | function calling 或纯代码 | **混合** | 纯代码无法 constrained decoding |
| 工具扩展 | 硬编码规则 | **加通用能力（如看图自检）** | 硬编码让系统僵化 |
| 训练 | RL 微调 | **不做 RL**，无参数在线学习 | MCP 让动作空间不固定；Bitter Lesson |
| 评测 | 公共 benchmark | **用户评分 + 内部自动化 + 人类评品味** | GAIA 与用户喜好不对齐 |
| 执行 | 共享容器 | **一 session 一 microVM** | 通用性的代价是攻击面 |
| 人机边界 | 只在出错时打断 | **沙箱可暂停**：确认 / 凭证 / CAPTCHA | 世界的摩擦是制度性的 |

一句话读法：**别的系统在「怎么让模型更聪明」上加东西，Manus 在「怎么让模型的缓存不失效」上删东西。**

---

## 附：本仓库文件

```
docs/
├── Manus 架构实践总结.md                                                        # ← 本文
├── 补充材料笔记.md                                                              # 第二批四篇的阅读笔记
├── Manus Agent 架构 · 完整参考资料清单.md                                        # 82 条链接 + 分级
├── Context Engineering for AI Agents with LangChain and Manus（中英文对照）.md   # webinar 转写
├── manus-context-engineering.md                                                # 官博全文整理
└── _extract-webinar.md                                                         # 转写的结构化提取（含行号锚点）
```

**本文的第二批来源**（读于 2026-10-10）：

- 极客公园《[Manus 最新对话全文](https://w.geekpark.net/news/352981)》— 2025-08-22，Stripe Tour 对谈
- E2B《[How Manus Uses E2B](https://e2b.dev/customers/how-manus-uses-e2b-to-provide-agents-with-virtual-computers)》— 2025-05-06
- AWS《[Manus Selects AWS](https://press.aboutamazon.com/aws/2025/12/manus-selects-aws-to-power-general-purpose-ai-agent-serving-millions-globally)》— 2025-12-03
- philschmid《[Context Engineering Part 2](https://www.philschmid.de/context-engineering-part-2)》— 2025-12-04
