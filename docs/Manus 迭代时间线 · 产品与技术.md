# Manus 迭代时间线：产品 / 商业 + 技术架构

> 本文把两条线分开整理：
> **① 产品与商业时间线**——发了什么功能、营收与公司层面的关键节点；
> **② 技术架构升级时间线**——每一代 harness / 执行层 / 上下文层改了什么。
> 第三节再逐阶段拆开讲「改了什么 → 效果是什么 → 证据在哪」。
>
> 来源分级沿用 [完整参考资料清单](Manus%20Agent%20架构%20·%20完整参考资料清单.md)：**A** = 官方一手，可当事实引用 · **B** = 可信非官方 · **C** = 第三方观点（媒体/安全研究） · **D** = 避开。
> 标 **⚠️** 的是口径冲突或未核实项。本文的产品日期以**官方博客页面自身标注的日期**为准。

---

## 一、产品与商业时间线

### 1.1 主表

| 日期 | 事件 | 类型 | 关键数字 / 事实 | 分级 |
|---|---|---|---|---|
| 2025-03-05·06 | Manus 上线（邀请制） | 发布 | 通用 agent 形态首次亮相，上线即刷屏 | A |
| 2025-03-10 | 系统提示词泄露 | 事件 | 29 个工具完整 schema + planner / knowledge / datasource 三模块 | B |
| 2025-03-13 | 等候名单暴涨 | 增长 | **7 天内 200 万人**（鞭牛士转述 Manus 官方；另见 [PingCAP](https://www.pingcap.com/case-study/manus-agentic-ai-database-tidb/)）⚠️ 官方原帖未取到 | B |
| 2025-04 | V4 版提示词流出 | 事件 | **27 个工具**，与 E2B 案例口径一致 | B |
| 2025-05-12 | 正式公开上线 | 发布 | 此前为邀请制；数据库迁移在 5 月初两周内完成 | A |
| 2025-07-18 | 官博《Context Engineering》 | 技术品牌 | 第一篇系统性架构自述，KV-cache 设计公开 | A |
| 2025-07-31 | **Wide Research** 发布 | 功能 | 几百个同构 agent 并行；首发 Pro，后续下放 Plus / Basic | A |
| 2025-08-22 | 极客公园 Stripe Tour 对谈 | 品牌 | Agent 三段定义；**RRR ≈ 9000 万美元**（肖弘澄清：RRR = 当月收入 ×12，≠ 现金收入） | A |
| 2025-10-16 | **Manus 1.5** + **1.5-Lite** | 发布 | 重架构引擎；15min → **<4min**；质量 +15%、满意度 +6%；全栈 Web 应用、协作、资料库、桌面/移动端 | A |
| 2025-10-31 | Manus for Slack | 功能 | 工作区协作入口 | A |
| 2025-11-11 | Notion MCP 用例 + Stripe 集成 | 功能 | 第三方服务接入开始密集 | A |
| 2025-11-19 | 与 Microsoft / Agent365 合作 | 生态 | 企业分发渠道 | A |
| 2025-12-05 | Slides + Nano Banana Pro | 功能 | 演示文稿质量升级 | A |
| 2025-12-15 | **Manus 1.6**（Max / Mobile Dev / Design View） | 发布 | 更新**核心 agent 架构**；新增旗舰档 **1.6 Max**；双盲测试满意度 **+19.2%**；可做移动应用 | A |
| 2025-12-17 | 官博《$100M ARR, $125M revenue run-rate》 | 商业 | 上线 8 个月 ARR 破 **1 亿美元**、总收入运行率 **>1.25 亿**；累计 **147 万亿 token**、**8000 万台虚拟电脑**；上线前融资 **$75M**（Benchmark 领投）；**105 人**（新加坡 / 东京 / 旧金山） | A |
| 2025-12-19 | Projects 接入 Connectors | 功能 | 项目可连外部服务 | A |
| 2025-12-29 | **Manus Joins Meta** | 组织 | 官宣加入 Meta；Meta 公告同日发布，**双方均未披露金额**，媒体口径约 **20 亿美元** | A（官宣）/ C（金额） |
| 2026-01-08 | 商务部表态评估该收购 | 监管 | 称将评估其与出口管制等法规的一致性 | B |
| 2026-03-16 | **My Computer** | 发布 | 从云端 VM 延伸到本地桌面 agent（macOS + Windows） | A |
| 2026-04-27 | **国家发改委禁止该收购** | 监管 | NDRC 决定「禁止投资，要求当事人撤销该收购交易」（[官方](https://zfxxgk.ndrc.gov.cn/web/iteminfo.jsp?id=20623)） | A |
| 2026-04-30 | **Cloud Computer** 首次发布 | 功能 | 常驻专属环境，「降低构建门槛」 | A |
| 2026-05~08 | Connectors 生态密集扩张 | 功能 | Shopify / Zoom / ElevenLabs / Higgsfield MCP / 多 Gmail+日历 等；5-05 起系统主动推荐 Connectors | A |
| 2026-05-18 | Scheduled Tasks 2.0 | 功能 | 定时任务升级，为 Automations 铺垫 | A |
| 2026-06-18 | 报道：原投资方拟约 **20 亿美元**从 Meta 回购 | 组织 | 原始出处 The Information，正文未取到 | C（未核实） |
| 2026-06-24 | Hosting Modes | 功能 | 按项目匹配服务器形态 | A |
| 2026-07-09 | **Branch**（一会话多分支） | 功能 | 从任意消息分出并行会话，继承全部上下文/文件/指令 | A |
| 2026-07-13 | Auto-Publish | 功能 | 建一次、持续发布 | A |
| 2026-07-14 | PowerPoint Slides | 功能 | 原生 PPT 产出 | A |
| 2026-07-22 | **Plan Mode** | 功能 | 执行前的人工评审门：生成结构化 Markdown 计划，用户确认后才动手，可中途开启 | A |
| 2026-08-11 | 《A Note to Our Users》 | 组织 | 说明与 Meta 分离；受影响账号 8/23–24 删除、8/25 起恢复数据 | A |
| 2026-09-01 | **Manus Resumes Independent Operations** | 组织 | 正式恢复独立运营，创始团队继续领导 | A |
| 2026-09-28 | **Manus 2.0** + **Cue** | 发布 | **Cascade** 新 harness、Cloud Computer、**Automations**、**Manus Studio**（Video Editor / Game Dev / Remote Control）；同日发布独立 App Cue | A |
| 2026-09-29 | Manus Flex | 功能 | 工作区内自带 API key | A |
| 2026-10-01 | Video Editor / Game Dev 细节发布 | 功能 | 2.0 的两个专业环境正式拆开讲 | A |
| 2026-10-01 | Salt Labs 劫持研究 | 安全 | 一封邮件劫持 agent；护栏检测晚于执行 | C |
| 2026-10-08 | 母公司蝴蝶效应新一轮融资 | 融资 | 称 **>$500M**、投后约 **$4B**（二手口径） | C（未核实） |

### 1.2 商业数据点

| 时点 | 指标 | 数值 | 出处 |
|---|---|---|---|
| 2025-08 | 收入运行率（RRR） | ≈ **$90M** | 极客公园对谈（A） |
| 2025-12-03 | 收入运行率 | **$90M**（订阅上线 4 个月后） | AWS 新闻稿（A） |
| 2025-12-17 | ARR | **$100M**，8 个月，自称为全球最快 0→$100M | 官博（A） |
| 2025-12-17 | 总收入运行率 | **>$125M**（含 usage-based） | 官博（A） |
| 2025-12-17 | 增速 | Manus 1.5 发布后**月环比 >20%** | 官博（A） |
| 2025-12-17 | 累计用量 | **147 万亿 token** / **8000 万台虚拟电脑** | 官博（A） |
| 上线前 | 融资 | **$75M**，Benchmark 领投（官方原文「Prior to our launch」；二手报道多记为 2025-04，投后约 $500M ⚠️ 口径不一致） | 官博（A）/ 二手（C） |
| 2025-12-17 | 人员 | **105 人**，新加坡 / 东京 / 旧金山（巴黎筹备中） | 官博（A） |
| 2025-12-29 | 被收购 | 约 **$2B**（⚠️ 媒体口径，官方双方均未披露金额，来源间有 $2B~$2.5B 差异） | Bloomberg/CNBC/WSJ（C） |
| 2026-06-18 | 回购传闻 | 原投资方拟约 **$2B** 买回（The Information，正文未取到） | Reuters 标题（C，未核实） |
| 2026-10-08 | 新融资 | **>$500M**，投后约 **$4B**（二手；官方未确认） | 证券时报 / Dealroom（C，未核实） |

**⚠️ 日期修正**：仓库原有材料把 100M ARR 记作 2025-12-28（HN 帖日期）。**官方博客页面自身标注为 2025-12-17（Wednesday, December 17）**，本文以博客日期为准。

**阶段读法**：2025-03 邀请制上线 → 05 公开 → 07~08 上下文工程 + 并行 → 10~12 连续两代引擎（1.5 → 1.6）+ 商业顶点（$100M ARR → 加入 Meta）→ 2026-04 监管反转 → 04~07 功能密集扩张 → 08~09 独立 → 09-28 2.0 换代。

---

## 二、技术架构升级时间线

| 日期 | 架构事件 | 层 | 关键变化 | 分级 |
|---|---|---|---|---|
| 2025-03 | 初版架构 | 执行 + 动作空间 | 单 VM 沙箱 + 扁平工具表（29 工具）；planner / knowledge / datasource 三模块 | B |
| 2025-04 | V4 提示词 | 动作空间 | 收敛到 27 工具；`todo.md` 复述范式仍在 | B |
| 2025-05 | **弃 Docker，转 Firecracker microVM**（E2B） | 执行层 | 容器不是完整 OS、装不了包；microVM 约 **150ms** 启动；E2B 自托管 | B |
| 2025-05 初 | 数据库迁 TiDB | 记忆层 | 两周迁移；上下文 append 落库，成为长期记忆底座 | A |
| 2025-07-18 | **官方公开 KV-cache 设计** | 上下文层 | 100:1 输入输出比；遮蔽而非移除；文件系统当上下文；复述操控注意力；保留错误 | A |
| 2025-07-31 | **Wide Research / Agentic MapReduce** | 协作层 | 同构子 agent + 独立 context；主 agent 定义输出 schema；`Submit Result` + constrained decoding | A |
| 2025-10-16 | **1.5 重架构引擎** | 全局 | 15min → <4min；质量 +15%；单任务上下文窗口扩大 | A |
| 2025-10（webinar） | **三层动作空间 + 压缩分层** | 动作空间 / 上下文 | Level 1 function calling / Level 2 sandbox CLI / Level 3 packages+APIs；compaction 优先；planner 以 agent-as-tool 取代 `todo.md` | A |
| 2025-11 | Action Engine 公开 | 循环 | Plan → Execute → Verify；最多 15,000 次迭代；TiDB X Branching 支撑 swarm | A |
| 2025-12-03 | **AWS 技术栈定型** | 模型 / 执行 / 数据 | Bedrock 统一治理；Firecracker + E2B 集群调度；Aurora Serverless + EKS + Glue + MSK | A |
| 2025-12-15 | **1.6 核心 agent 架构更新** | 全局 | 「updated the core agent architecture to handle more complex work with significantly less supervision」；Max 架构；Wide Research 全部子 agent 改跑 Max | A |
| 2026-04-30 | **Cloud Computer** | 执行层 | 常驻专属环境（不再是一 session 一 VM 的短生命周期形态） | A |
| 2026-07-09 | **Branch** | 上下文层 | 从任意消息 fork 一个并行会话，继承全部历史/文件/指令；各分支上下文互相隔离 | A |
| 2026-07-22 | **Plan Mode** | 循环 / HITL | 执行前的评审门：生成结构化 Markdown 计划，用户确认或驳回后才执行；可中途插入 | A |
| 2026-09-28 | **Cascade（Manus 2.0 自研 harness）** | Harness 层 | 「从轻开始，按需引入专用能力」；一组实测配置 token −23.2%、时间 −28.2%、成本 −32% | A（自报）/ ⚠️ 无独立验证 |
| 2026-09-28 | Automations | 触发层 | 从定时任务扩展到事件触发（邮件、广告、日历、Slack、Notion） | A |

---

## 三、逐阶段：技术改进点与效果

### 阶段 0 · 2025-03 ~ 04：单 VM + 扁平工具表

**改了什么**
- 一个 session 一台沙箱 VM；工具是一张扁平表（29 → 27 个），全部走 function calling。
- 顶层三个模块：planner / knowledge / datasource。
- 规划用 `todo.md`：模型反复重写待办清单，把目标「复述」到上下文末尾。

**效果 / 问题**
- 复述机制确实能对抗「lost in the middle」，但本身极贵：3–4 月日志里约 **1/3 的动作**是在更新待办列表。
- 工具表扁平 + 数量增长 → 上下文前部不稳定，是后续所有改动的直接动因。

**证据**：泄露件（B）；`todo.md` 量化见 [_extract-webinar.md:206](_extract-webinar.md#L206)（A）。

---

### 阶段 1 · 2025-05：执行层换成 Firecracker microVM

**改了什么**
- 弃用 Docker，改用 Firecracker microVM（E2B 集群调度），并自托管。
- 沙箱提供**完整 OS 能力**：可现装 app / Python 包；付费用户沙箱最长保存 14 天；支持暂停/恢复。

**效果**
- 启动从 Docker 的 **10–20 秒**降到约 **150ms**（2025-05）→ 后为 **125ms**（2025-12）。
- 关键不是快，而是**完整 OS 让「沙箱里现装现用」成立**——这是后面 Level 3 动作空间（写脚本调包/API）的前提（[补充材料笔记.md:67-70](补充材料笔记.md#L67-L70)）。
- 一 session 一 VM 换来通用性，代价是每个 VM 都是可横向移动的完整环境（Salt Labs 邮件劫持、AuraLabs SilentBridge 两个安全研究都指向这一点，见[参考资料清单](Manus%20Agent%20架构%20·%20完整参考资料清单.md)第七节）。

**证据**：E2B 案例（B）、AWS 新闻稿（A）。

---

### 阶段 2 · 2025-07：把 KV cache 变成硬约束（上下文工程成型）

**改了什么**
- **前缀稳定 + append-only**：不改历史动作/观察，序列化必须确定（JSON 键序也不能变），必要时显式标 cache breakpoint。
- **遮蔽而非移除**：工具可用性用「上下文感知状态机 + 解码期 mask logits」管理，不动态增删工具定义。
- **文件系统当上下文**：工具输出落盘，历史里只留引用（路径 / URL / query），需要时再读回来。
- **压缩分级**：Compaction（可逆）优先于 Summarization（有损）。
- **保留错误**：失败动作和堆栈留在上下文里，不重试不清理。
- **对抗 few-shot 固化**：动作/观察里引入受控的格式多样性。

**效果**
- 主指标是 **KV-cache 命中率**；平均输入/输出 token 比约 **100:1**，缓存与未缓存单价差 10 倍（$0.30 vs $3 / MTok）。
- 压缩后仍需保留「最近几次工具调用的原格式全文」，否则模型会模仿 compact 格式输出缺字段的调用。
- 腐化触发点：模型硬上限 1M，**约 200k 起退化**，用 **128k–200k** 触发缩减（⚠️ 与 philschmid 的 < 256k 口径并存）。

**证据**：官博（A）+ 转写（A）。

---

### 阶段 3 · 2025-07-31：并行子 agent（Wide Research）

**改了什么**
- 内部叫 **Agentic MapReduce**：主 agent 生成大量**同构**子 agent，各自独立 context；每个子 agent 是完整通用 Manus 实例，不是角色化 worker。
- 共享同一个沙箱：主 → 子只传文件路径，不复制内容。
- 回收靠约束：主 agent 必须先定义输出 schema，子 agent 用 `Submit Result` + constrained decoding 提交，结果相当于一张被 schema 约束的电子表格。
- 同时给出上下文隔离二分法：**by communicating**（只给指令，便宜）vs **by sharing memory**（给全量轨迹，但 system prompt/动作空间不同 → KV cache 无法复用，必须付全价）。

**效果**
- 把「上下文隔离」变成可工程化的批量模式，同时避开多 agent 互相同步的泥潭。
- 反面代价写得很清楚：**fork 上下文 = 打断缓存**，共享上下文要当昂贵依赖最小化。

**证据**：[Wide Research 官博](https://manus.im/en/blog/introducing-wide-research)（A）、转写 行 1273（A）、philschmid（B）。

---

### 阶段 4 · 2025-10-16：Manus 1.5 重架构引擎

**改了什么**
- 「重新架构的引擎」——不是加功能，是换底。
- 引入按任务分层的模型档位：**Manus-1.5**（最新架构全功能）与 **Manus-1.5-Lite**（成本优化）。
- 单任务可分配**额外推理时间与算力**；单任务**上下文窗口扩大**（后由 TiDB X 承接为「Unlimited Context」）。

**效果**
- 任务完成时间：4 月的 15 分钟 → **不到 4 分钟**。
- 内部基准：任务质量 **+15%**、用户满意度 **+6%**。
- 全栈 Web 应用的落点是「一次会话、一个共享上下文」跑完整条价值链。

**证据**：[Manus 1.5 官博](https://manus.im/zh-cn/blog/manus-1.5-release)（A）；TiDB 侧承接见 [PingCAP](https://www.pingcap.com/case-study/manus-agentic-ai-database-tidb/)（A）。

---

### 阶段 5 · 2025-10 ~ 12：三层动作空间、循环与数据层

**改了什么**
- **三层动作空间**：Level 1 固定原子函数（约 20 个，schema safe）→ Level 2 沙箱 CLI（含 `mcp-cli`，MCP 工具**不注入** function calling 空间）→ Level 3 写 Python 调预授权 API/包。三层从模型视角看仍是标准 function call。
- **规划换代**：`todo.md` → agent-as-tool 的独立 planner agent（可换用不同模型做外部视角审查）。
- **循环定型**：Action Engine = Plan → Execute → Verify，每次 action + observation 追加并持久化。
- **数据层**：TiDB X 当长期记忆；用 **Database Branching** 支撑 agent swarm（可 1 万级临时分支）。
- **模型层**：走 Amazon Bedrock 统一治理，按场景选模型；明确不用开源模型，理由是成本而非质量。

**效果**
- 混合动作空间替代纯 CodeAct：曾全量试过 CodeAct，因代码**无法 constrained decoding** 而放弃。
- 工具发现成本被压到很低：system prompt 只给极紧凑的工具名列表 + `--help` 约定，不教用法。
- 不用索引库/向量库：每个 session 的沙箱都是新的，来不及建索引，直接 grep/glob。
- AWS 侧硬数字（2025-12）：沙箱启动约 **125ms**、单沙箱内存约 **5MiB**、单机**数千**并发、**3 人管数万沙箱**、开发运维人力 **−60%**、运营效率 **+70%**、计算成本 **−68%**。

**证据**：webinar 转写（A）、philschmid（B）、AWS 新闻稿（A）、PingCAP（A）。

---

### 阶段 6 · 2025-12-15：Manus 1.6（同为「换引擎」级别的一代）

**改了什么**
- 官方措辞是**更新核心 agent 架构**，「handle more complex work with significantly less supervision」。
- 新增旗舰档 **Manus 1.6 Max**：规划与问题求解架构更先进，主打**一次成功率**与自主性。
- **Wide Research 全部子 agent 改跑 Max 架构**——这是协作层与模型档位的绑定。
- 同期功能：Mobile Development（自然语言建移动应用）、Design View（可点选编辑的图像画布）。

**效果**
- **双盲测试用户满意度 +19.2%**（官方口径）。
- 电子表格类任务提升最明显；Web 开发的 UI / 功能 / 交互同步改善。
- 内部基准「跨类别全面提升」，最明显的增益集中在**多步、高精度**任务。

**证据**：[Introducing Manus 1.6](https://manus.im/en/blog/manus-max-release)（A，自报）。⚠️ 这一代在仓库原有材料里**完全缺失**，是本次补进来的。

---

### 阶段 7 · 2026 上半年：执行层出云 + 上下文可分支 + 执行前评审

**改了什么**
- **2026-03-16 My Computer**：从云端 VM 延展到本地桌面，Manus 在被授权的可见工作区里用你的文件、浏览器和应用（Computer Use）。
- **2026-04-30 Cloud Computer**：常驻专属环境，支持「笔记本合上也不停」的项目与全天候自动化。
- **2026-07-09 Branch**：从任意一条消息分出一个并行会话，继承截至该点的**全部指令、文件与历史**；原会话不受影响，各分支上下文互相隔离。「同一份上下文可以花很多次而不耗尽」。
- **2026-07-22 Plan Mode**：手动开启的执行前评审门——先出结构化 Markdown 计划（目标 / 步骤 / 约束），用户确认或驳回后才执行；可中途开启，「Manus will not start building until you confirm」。

**效果 / 意义**
- 执行层第一次不再只有「一 session 一 VM」一种形态（本地 + 常驻云）。
- 这两个功能是对「上下文稀缺 + 试错昂贵」的正面答案：Branch 用隔离换并行探索，Plan Mode 把纠错从「建成之后」前移到「动手之前」。
- 与 Wide Research 的思路一致但层级不同：Branch 是**会话级 fork**，Wide Research 是**系统级并行**。

**证据**：[My Computer](https://manus.im/blog/manus-my-computer-desktop)、[Cloud Computer](https://manus.im/blog/manus-cloud-computer)、[Branch](https://manus.im/blog/manus-branch)、[Plan Mode](https://manus.im/blog/manus-plan-mode)（均 A）。

---

### 阶段 8 · 2026-09-28：Cascade（Manus 2.0 的自研 harness）

**改了什么（官方口径）**
- 唯一一句实质描述：harness 让项目**从轻开始**，只有工作需要时才引入专用能力（“keeps a project light from the start and brings in specialized capabilities only when the work needs them”）。brief / page / video / automation 留在同一个项目里。
- 同批：Cloud Computer（2.0 里可购买）、Automations（事件触发）、Studio（按项目引入能力，避免把全部能力带进每个任务）、Cue（每个 agent 有自己的邮箱、电话、钱包和电脑）。

**效果**
- 官方给一组实测配置：token **−23.2%**、时间 **−28.2%**、成本 **−32%**。

**⚠️ 关键限制**
- 参考资料清单已标注：**「Cascade 独立验证：不存在」**（[完整参考资料清单.md:285](Manus%20Agent%20架构%20·%20完整参考资料清单.md#L285)）。
- 「按需引入专用能力」在方向上和三层动作空间 / Skills 渐进披露同源，但**这是推断，不是证据**。
- 结论：前七个阶段的设计在 Cascade 里保留了多少、推翻了什么，公开材料无法判定。**仓库里那套「KV cache 硬约束」描述，主体属于 Cascade 之前的架构。**

**证据**：[Introducing Manus 2.0](https://manus.im/blog/introducing-manus-2-0)（A，自报）。

---

## 四、效果指标一览

| 指标 | 数值 | 时点 / 阶段 | 出处 | 可用性 |
|---|---|---|---|---|
| 输入 / 输出 token 比 | 约 100:1 | 2025-07 | 官博 | A |
| 缓存 vs 未缓存单价 | $0.30 vs $3 / MTok（10×） | 2025-07 | 官博 | A |
| 上下文腐化触发点 | 128k–200k | 2025-10 | 转写 行 569 | A ⚠️ 另一口径 < 256k |
| todo 更新占动作比例 | 约 1/3 | 2025-03~04 日志 | 转写 行 1513 | A（轶事性） |
| 沙箱启动 | ~150ms → ~125ms | 2025-05 → 2025-12 | E2B → AWS | A（取较新值） |
| 单沙箱内存 | 约 5 MiB | 2025-12 | AWS | A |
| 单机并发沙箱 | 数千 | 2025-12 | AWS | A |
| 运维人效 | 3 人管数万实例 | 2025-12 | AWS | A |
| 开发运维人力 / 运营效率 / 计算成本 | −60% / +70% / −68% | 2025-12 | AWS | A |
| 任务完成时间 | 15min → <4min | 2025-04 → 2025-10 | Manus 1.5 官博 | A |
| 任务质量 / 满意度 | +15% / +6% | 2025-10 | Manus 1.5 官博 | A（内部基准） |
| 1.6 满意度 | **+19.2%**（双盲） | 2025-12 | Manus 1.6 官博 | A（自报） |
| 架构重写次数 | 6 个月内 5 次 | 2025-03~10 | 转写 行 1065 + philschmid | A（两处印证） |
| 单任务最大迭代 | 最多 15,000 次 | ~2025-11 | PingCAP | A |
| Cascade 三指标 | token −23.2% / 时间 −28.2% / 成本 −32% | 2026-09 | Manus 2.0 官博 | A（自报，无独立验证） |
| 累计用量 | 147 万亿 token / 8000 万台虚拟电脑 | 2025-12-17 | 官博 | A |
| **KV-cache 命中率** | **无一手数字** | — | — | ⚠️ 转写与四篇新材料都没给 |

---

## 五、冲突与未核实项

1. **100M ARR 日期**：官方博客 **2025-12-17** vs HN 帖 **2025-12-28**。本文取博客日期。
2. **腐化阈值**：128k–200k（转写，触发点口径）vs < 256k（philschmid，退化起点口径）。不归一，两个都列。
3. **沙箱启动**：150ms（2025-05）vs 125ms（2025-12）。取较新的 125ms。
4. **工具数**：27（E2B + 泄露 V4）vs 约 20 个核心原子函数（philschmid）vs 30（转写自称「随机数」）。三者不是同一口径。
5. **Cloud Computer 时点**：2026-04-30 首次发布，2026-09-28 在 2.0 里作为可购买能力再包装。仓库原有材料只记了后者。
6. **Manus 1.6 缺失**：仓库原有材料从 1.5（2025-10）直接跳到 2.0（2026-09），漏掉了 1.6（2025-12-15）这一代。
7. **Cascade**：只有官方一句话 + 一组自报指标，无任何第三方验证或逆向。
8. **webinar 转写覆盖度**：文件头标 1h52s，最后时间戳只到 01:00:43，可能只覆盖约一小时。
9. **收入口径**：RRR（当月收入 ×12）≠ 现金收入；$90M（2025-08 / AWS 2025-12）与 $100M ARR + $125M RRR（2025-12-17）时点不同。
10. **Meta 交易金额**：官方（Manus 与 Meta）**均未披露金额**。约 20 亿美元只是媒体口径，且来源间有 $2B~$2.5B 差异 → 引用时必须标为「据媒体报道」。
11. **博客正文不显示年份**：单篇文章页只写「分类·星期, 月份 日」，年份靠图片 CDN 路径与星期吻合推断。`projects-connectors` 一页的首图（2025-12-16）与题图（2025-12-23）路径都和文章标注日期（12-19）不一致，属官方页面自身不留痕导致的推断风险。
12. **2025-04 融资时点矛盾**：官方写「上线之前」，二手报道写 2025 年 4 月。估值 $500M 与投资方名单（腾讯 / 真格 / HSG）只见于二手。
13. **未核实的收入数字**：The Information（2026-06）据称给出年化运行率 **$400–500M**、2026-10-08 轮 **>$500M / 投后 $4B**——均只见二手转述，官方未确认，本文仅列不采用。
14. **不可采信的聚合源**：`multiples.vc` 同时给出「$2.5B 收购」「2025-04 收入 $100M」等互相冲突的数字，其中后者与官方「上线 8 个月破 $100M ARR」直接矛盾；36kr 专栏文（访问量等数据）亦未核实。

---

## 附：本文主要来源

**仓库内**
- [Manus 架构实践总结.md](Manus%20架构实践总结.md) · [_extract-webinar.md](_extract-webinar.md) · [补充材料笔记.md](补充材料笔记.md) · [manus-context-engineering.md](manus-context-engineering.md) · [完整参考资料清单.md](Manus%20Agent%20架构%20·%20完整参考资料清单.md)

**官方博客 / 新闻稿**
- [Context Engineering for AI Agents](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)（2025-07-18）
- [Introducing Wide Research](https://manus.im/en/blog/introducing-wide-research)（2025-07-31）
- [Manus 1.5](https://manus.im/zh-cn/blog/manus-1.5-release)（2025-10-16）
- [Manus for Slack](https://manus.im/blog/manus-slack-integration)（2025-10-31）
- [Manus with Notion MCP](https://manus.im/blog/manus-notion-mcp-use-cases) · [Manus with Stripe](https://manus.im/blog/manus-stripe)（2025-11-11）
- [Microsoft / Agent365](https://manus.im/blog/manus-microsoft-agent365)（2025-11-19）
- [Manus Selects AWS](https://press.aboutamazon.com/aws/2025/12/manus-selects-aws-to-power-general-purpose-ai-agent-serving-millions-globally)（2025-12-03）
- [Slides + Nano Banana Pro](https://manus.im/blog/manus-slides-nano-banana-pro)（2025-12-05）
- [Introducing Manus 1.6](https://manus.im/en/blog/manus-max-release)（2025-12-15）
- [$100M ARR / $125M RRR](https://manus.im/blog/manus-100m-arr)（2025-12-17）
- [Manus Joins Meta](https://manus.im/blog/manus-joins-meta-for-next-era-of-innovation)（2025-12-29）
- [Projects + Connectors](https://manus.im/en/blog/projects-connectors)（2025-12-19）
- [My Computer](https://manus.im/blog/manus-my-computer-desktop)（2026-03-16）
- [Cloud Computer](https://manus.im/blog/manus-cloud-computer)（2026-04-30）
- [Scheduled Tasks 2.0](https://manus.im/blog/manus-schedules)（2026-05-18）· [Hosting Modes](https://manus.im/blog/manus-hosting-web-builder)（2026-06-24）
- [Branch](https://manus.im/blog/manus-branch)（2026-07-09）· [Auto-Publish](https://manus.im/blog/manus-auto-publish)（2026-07-13）· [PowerPoint Slides](https://manus.im/blog/manus-ppt-slides)（2026-07-14）· [Plan Mode](https://manus.im/blog/manus-plan-mode)（2026-07-22）
- [A Note to Our Users](https://manus.im/blog/a-note-to-our-users)（2026-08-11）· [Manus Resumes Independent Operations](https://manus.im/blog/manus-resumes-independent-operations)（2026-09-01）
- [Introducing Manus 2.0](https://manus.im/blog/introducing-manus-2-0)（2026-09-28）· [Manus Flex](https://manus.im/blog/introducing-manus-flex)（2026-09-29）· [Video Editor](https://manus.im/blog/introducing-video-editor) · [Game Dev](https://manus.im/blog/introducing-game-developer)（2026-10-01）

**监管与二手来源**
- [NDRC：禁止外资收购 Manus 的决定](https://zfxxgk.ndrc.gov.cn/web/iteminfo.jsp?id=20623)（2026-04-27，官方）
- [新华社报道](http://www.xinhuanet.com/20260427/437b8982e934427ca95bcf3628fb6583/c.html)（2026-04-27）· [证券时报（商务部表态 / 2026-10 融资）](https://stcn.com/article/detail/4208272.html)
- [Reuters：原投资方拟 $2B 回购（标题页）](https://www.reuters.com/5f374b32b07b/world/asia-pacific/manus-original-investors-plan-buy-back-ai-firm-meta-2-billion-information-2026-06-18/)（2026-06-18，正文未取到）
- [Dealroom 转述](https://dealroom.co/news/info-tugym8-manus-raises-more-than-500-million-after-unwinding-meta-deal/)（引 The Information，未核实）
- [鞭牛士：等候名单 200 万](https://www.bianews.com/news/details?id=207545)（2025-03-13，二手）

**第三方**
- [PingCAP × Manus 案例研究](https://www.pingcap.com/case-study/manus-agentic-ai-database-tidb/)（~2025-11）
- [E2B：How Manus Uses E2B](https://e2b.dev/customers/how-manus-uses-e2b-to-provide-agents-with-virtual-computers)（2025-05-06）
- [philschmid：Context Engineering Part 2](https://www.philschmid.de/context-engineering-part-2)（2025-12-04）
- [Salt Labs](https://salt.security/blog/how-we-hijacked-an-ai-agent-with-a-single-email)（2026-10-01）
