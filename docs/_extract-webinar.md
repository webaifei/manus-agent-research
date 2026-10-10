# Webinar 结构化抽取：Context Engineering for AI Agents with LangChain and Manus

来源：`Context Engineering for AI Agents with LangChain and Manus（中英文对照）.md`（1910 行）
说话人：Speaker 1 = Lance（LangChain 创始工程师，主持/提问者）；Speaker 2 = 季逸超 Yichao "Peak" Ji（Manus 联合创始人兼首席科学家，主讲人）。
结构：行 53–317 为 Lance 的铺垫 + 五大主题概览；行 325–877 为 Peak 的正式演讲；行 885–1901 为 Q&A。
以下所有锚点均为该文件中 EN/ZH 行的真实行号。**文中未说明**表示转录没有给出理由。

---

## 1. 架构主张

### 1.1 上下文缩减：compaction（可逆）vs summarization（不可逆）
- 主张：Manus 把「上下文缩减」拆成两种操作——compaction（压缩）与 summarization（总结），并明确区分。压缩可逆，总结不可逆。理由：智能体基于历史动作做链式预测，无法预知哪个旧动作在 10 步后突然重要，因此必须保留可恢复性。行 521、行 553、行 569。
- 主张：每个工具调用与工具结果都有**两套格式**：full format 与 compact format。compact 版本先剥离一切能从文件系统或外部状态重建的信息。行 537。
- 举例：写文件工具有 path 和 content 两个字段；工具返回后文件已存在于环境，因此 compact 格式安全地丢掉超长的 content，只留 path；需要时按 path 重新读取，信息并未真正丢失，只是外部化。行 537。
- 主张：压缩只压缩**最旧的约 50% 工具调用**，较新的保持完整细节。理由：模型需要新鲜的 few-shot 示例学习正确工具用法；否则最坏情况下模型会模仿 compact 格式、输出缺字段的调用。行 585。
- 主张：顺序上**先 compaction，后 summarization**，不是反过来。行 521、行 585。
- 主张：多轮压缩后要检查实际回收了多少空闲上下文；若收益很小，才转向 summarization。行 601。
- 主张：summarization 永远使用 full 版本数据（不是 compact 版本），并且保留最后几个工具调用/结果的全量细节。理由：让模型知道停在哪里、平滑续接；否则总结后模型会改变风格与语气。行 601。
- 主张：summarization 的提示**不要自由形式**，而是定义一个 schema（表单式字段，如「改过的文件」「用户目标」「我停在哪里」），让模型填空。理由：输出稳定、可迭代。行 1177。
- 主张：总结前先把上下文关键部分卸载到文件；有时更激进，把整个 pre-summary 上下文 dump 成文本/日志文件，以便日后恢复；grep 对日志文件同样有效。行 553。

### 1.2 上下文隔离：by communicating vs by sharing memory
- 主张：借用 Go 社区「不要通过共享内存通信，而要通过通信共享内存」的二分，把它平移到 agent 上下文。行 617。
- **by communicating（通信模式）**：主 agent 写 prompt，子 agent 的全部上下文只含该指令。适用：指令简短清晰、只有最终输出重要（如代码库搜片段）。主 agent 不关心子 agent 怎么找到。行 633。
- **by sharing memory / by sharing context（共享记忆）**：子 agent 能看到此前的全部上下文（含所有工具使用历史），但拥有**自己的 system prompt 和自己的动作空间**。适用：最终产出依赖大量中间搜索与笔记的深度研究场景。行 649、行 665。
- 主张：共享记忆模式的代价——每个子 agent prefill 更大（输入 token 花更多）；且因 system prompt 与动作空间不同，**无法复用 KV cache，必须付全价**。行 681。
- 反主张：把笔记/搜索存文件让子 agent 重读，会浪费延迟和上下文，甚至可能用更多 token。行 665。
- 主张：谨慎增加子智能体，因为「通信非常难」，更多把子智能体实现为 **agent as tool**。行 1577。

### 1.3 上下文卸载：分层动作空间（layered action space）
- 问题定义：集成 MCP 后工具本身占大量上下文，工具过多导致 **context confusion**，模型会调用错误工具甚至不存在的工具。行 697。
- 否定方案：对工具描述做动态 RAG（按任务/状态按需加载）有两个问题——(1) 工具定义位于上下文前部，**KV cache 每次重置**；(2) 模型对已移除工具的过往调用仍留在上下文中，会 few-shot 诱导模型调用无效工具/参数。行 713。
- 主张（Manus 正在试验）：三层抽象动作空间。行 713、行 729。
  - **Level 1 — function calling**：优点 schemasafe（得益于 constrained decoding）；缺点破坏缓存、工具过多引起混淆。行 729。
  - **Level 2 — sandbox utilities**：卸到下一层，通过 shell 调用预装实用工具。行 757、行 761。
  - **Level 3 — packages and APIs**：Manus 写 Python 脚本调用预授权 API 或自定义包。行 793。
- 主张：三层**从模型视角看仍然全部走标准 function calls**，因此接口保持简单、缓存友好、各函数正交（sandbox utilities 走 shell 函数；第三方 API 走写文件 + shell 执行）。行 809。
- 主张：Level 1 只保留**固定数量的原子函数**（读写文件、执行 shell、搜索文件/互联网、部分浏览器操作），理由是边界清晰、可组合成复杂工作流。行 745。
- 主张：Level 2 里包括格式转换器、语音识别工具，以及一个非常特殊的 **Manus MCP CLI**——**不把 MCP 工具注入 function calling 空间**，而是全部在沙箱内经命令行完成。行 777。
- Level 2 权衡：对大输出很好；对与前端低延迟的来回交互不好，因为需要把 agent 交互可视化给用户。行 777。
- Level 3 卖点：适合需要大量计算/内存、但不必把数据推入模型上下文的任务（如整年股价数据只回摘要）；API key 由 Manus 代用户购买并包含在订阅中。行 793。
- 对 Level 3 的反面说明：代码可组合（引 CodeAct），但**不 schema safe**，对代码做 constrained decoding 非常难。行 793、行 1433。
- 主张：Manus 曾尝试**完全使用 CodeAct**，因无法利用 constrained decoding 而放弃，改混合方式。行 1433、行 1465。

### 1.4 Agent loop / 整体定位
- 主张：「上下文工程是应用与模型之间最清晰、最实用的边界」，因此要坚定划线、不要过早自训模型。行 505、行 521。
- 主张：五个维度（offload / reduce / retrieve / isolate / cache）**不互相独立**：offload+retrieve 使更高效缩减成为可能；稳定检索让隔离安全；隔离减慢上下文腐化、降低缩减频率；但更多隔离与缩减会损害缓存效率与输出质量。行 825。
- 主张（收尾反调）：避免 context over-engineering；Manus 发布以来的最大飞跃来自**简化/删除技巧**，而非增加花哨的上下文管理层或检索 hack。行 841、行 857。
- 主张：Manus 的通用性来自「运行在计算机上，而计算机是图灵完备的」；有了 shell + 文件 + 文本编辑器就已是图灵完备，可把大量能力卸载到沙箱。行 1401。

### 1.5 规划器 / todo / 记忆
- 主张：Manus 起初用 **todo.md 范式**，但它浪费大量轮次与 token；现在改用更结构化的规划。行 1509。
- 主张：当前版本用 **planner agent**——系统内部底部的 planner 是「用 agent-as-tool 范式实现的一个工具」，即有一个独立 agent 管理计划。行 1513。
- 主张：最新版本**不再使用 todo.md**；但 todo.md 仍能工作、仍能出好结果。行 1513。
- 主张：规划用独立 agent（不同视角，可做外部审查），并且**规划可以换用不同模型**（举例 Grok 能给出有趣洞见）。行 1545。
- 主张：长期记忆叫 **knowledge**，是显式记忆；不会自动注入，而是弹窗让用户接受/拒绝。行 1033。
- 主张：正在探索更自动的记忆，例如数据可视化的 CJK 字体问题（用户反复纠正「用 Noto/CJK 字体」），希望利用这种**集体反馈**做无参数的自改进/在线学习。行 1033。
- 主张：**knowledge agent** 的职责是审查用户与 agent 的对话，决定什么应写入长期记忆。行 1609。

### 1.6 多智能体组织
- 主张：Manus **不按角色划分** agent（没有 designer agent / programming agent / manager agent），理由是那种划分源自人类公司的运作方式、源于人类上下文限制。行 1577。
- 主张：Manus 是 multi-agent 系统，但**只有很少几个 agent**：一个很大的 general executor agent、一个 planner agent、一个 knowledge management agent，也许还有 data API registration agent。行 1577。
- 明确否定拟人化组织架构图式的多智能体设计。行 1593（Lance 附和/观察），行 1577（Peak 原话）。

### 1.7 Sub-agent 通信与 Wide Research
- 主张：发布过 **Wide Research**（内部称 **Agentic MapReduce**，受 MapReduce 设计启发）。行 1273。
- 主张：主→子传信息的方式之一是**共享同一个 sandbox**——文件系统就在那里，只需传不同路径。行 1273。
- 主张：难点不在下发信息，而在回收正确输出。做法：主 agent 生成子 agent 时**必须自己定义输出 schema**；子 agent 侧有一个特殊工具 **Submit Result**，用 constrained decoding 保证提交内容符合该 schema；整体产出类似一张被 schema 约束的电子表格。行 1273。
- 主张：复杂搜索（多 query、需要聚合要点）用 sub agent / agent-as-tool，对模型看是一个叫 `advanced search` 的函数，触发的是有固定输出 schema 的子 agent。简单搜索（如 Google）则用 full detail 格式追加进上下文并依赖 compaction。行 1241。
- 主张：总是指示模型把中间洞见/关键发现写入文件，以防压缩比模型预期更早发生。行 1241。

### 1.8 沙箱、工具发现、检索
- 主张：**每个 Manus 会话运行在一个完整虚拟机沙箱中**，跑在自定制的 Linux 上。行 761。
- 主张：工具发现靠两件事——(1) system prompt 里给提示，说明预装 CLI 工具位于特定文件夹（类比 /usr/bin）；(2) 最常用的工具直接以**超紧凑**形式列进 system prompt。只列名字、不教怎么用，并告知可安全使用 `--help`，因为所有工具由自己团队开发、格式统一。行 969。
- 主张：**不使用索引数据库 / 不即时建向量库**。理由：每个 Manus 会话的沙箱都是新的，用户要快速交互，没有时间即时建索引；因此更接近 Claude Code，依赖 grep 和 glob。行 1001。
- 例外：若要长期记忆或集成企业知识库，仍须依赖外部/向量索引。行 1001。
- 主张：存储格式优先**基于行的格式**（便于 grep 和按行区间读取）；不特别偏好 markdown，反因模型被训练得太爱 markdown、易产出过多 bullet，而**更想用纯文本**。行 1145。
- 主张：几乎 Manus 的每个动作都是可逆的（只要能卸载到文件系统或外部状态），因为每类动作天然带唯一标识符：文件操作有 path，浏览器操作有 URL，搜索动作有 query。行 1209。

### 1.9 模型路由 / 模型选择
- 主张：**不使用任何开源模型**，原因不是质量而是成本：agent 场景输入远长于输出，KV cache 极重要；分布式 KV cache 在开源方案上很难实现，而前沿厂商有更稳固的全球分布式缓存基础设施；算下来旗舰模型有时比开源更便宜。行 1321。
- 主张：优先 Anthropic（最适合 agentic 任务），同时观察 Gemini（多模态）与 OpenAI（复杂数学/推理）；前沿实验室并未收敛到同一方向。行 1337。
- 主张：应用公司的优势是**不必绑定单一模型**，可做 task-level routing，甚至 subtask/step-level routing（前提是能处理 KV cache 复用问题）。行 1337。
- 主张：内部做大量评测来决定哪个子任务用哪个模型。行 1337。
- 主张：缓存管理依赖提供商能力（Lance 点名 Anthropic 的 prompt/input caching）。行 1353。
- 主张：**故意不与 Claude Code 用同名工具**，因为自家函数的需求与入参可能不同，同名会困惑模型。行 1817。

### 1.10 评测与护栏
- 主张：发布初期用 GAIA 等公共学术基准，上线后发现**严重不对齐**（GAIA 高分但用户不喜欢）。行 1737。
- 主张：现在用三类评测：(1) 每个完成会话请用户 1–5 星打分，最看重平均用户评分；(2) 内部自动化测试 + 自建可验证数据集 + 仍用公共基准 + 自建的**执行型/事务型**任务集（因有沙箱可频繁重置环境）；(3) 大量**人类实习生**评网站生成/数据可视化等「品味」类任务，因为很难设计判断视觉吸引力的 reward model。行 1737。
- 主张：不做 RL。理由：支持 MCP 就不是固定动作空间，非固定动作空间难以设计好 reward、rollout 不平衡；要做就等于自己造基础模型，而模型公司正在替你做。行 1769、行 1785。
- 主张：转向**无参数（parameter-free）** 的个性化/在线学习，例如利用集体反馈。行 1785。
- 主张（护栏）：沙箱连着互联网，「一切都很危险」；投入大量精力做护栏——不让信息离开沙箱，对出站流量做检查，防止 token 之类外泄；有「removing」机制。行 1641、行 1673。
- 主张：内置浏览器很复杂（可持久化登录态），网页内容可能恶意、做 prompt injection；认为这超出应用公司范围，因此与 computer-use 模型供应商（举例 Google）紧密合作。行 1689。
- 主张：所有敏感操作（浏览器内或沙箱内）都要求**人工确认**，否则用户须接管；这是渐进方案——模型自身护栏变好就可少做。行 1705。

### 1.11 为什么需要上下文工程（论证）
- 主张：上一家创业公司从零训练语言模型做开放域信息抽取/知识图谱/语义搜索，痛苦在于「产品创新速度被模型迭代速度完全限制」；结论是创业公司应**尽可能长时间依赖通用模型 + 上下文工程**，而非过早做专用模型。行 425。
- 反主张：产品成熟、开源基座变强后做微调是「另一个陷阱」——固定动作空间、围绕当前产品行为设计奖励、生成大量 on-policy rollout；但一切可能一夜之间改变。行 441、行 473。
- 关键反例：**MCP 的发布**把 Manus 从紧凑的静态动作空间改成近乎无限可扩展，而开放域问题极难优化。行 489。

---

## 2. 数字

| # | 数字 | 度量对象 | 锚点 |
|---|---|---|---|
| 1 | 1h 52s（头部标注）/ 最后时间戳 01:00:43 | 节目总时长标注 vs 转录实际覆盖终点 | 行 7 / 行 1893 |
| 2 | 2 位 | 说话人（Speaker 1 Lance / Speaker 2 Peak） | 行 53 / 行 69 |
| 3 | ~50 次 | 典型任务的工具调用次数（引 Manus 博客） | 行 121 |
| 4 | 数百轮（hundreds of turns） | Anthropic 所述生产智能体对话轮数 | 行 137 |
| 5 | 10 年以上 | Peak 在 NLP/构建语言模型领域的年限 | 行 425 |
| 6 | 1–2 周 | 上一家公司单次「训练 + 评估」周期 | 行 425 |
| 7 | 1,000,000 token | 模型硬性上下文上限（称今天很常见） | 行 569 |
| 8 | ~200k | 模型实际开始退化的位置 | 行 569 |
| 9 | 128k–200k | 「context rot 阈值」区间，用作缩减触发点 | 行 569 |
| 10 | 50% | 被 compact 的最旧工具调用比例 | 行 585 |
| 11 | 3 层 | 分层动作空间抽象层级（function calling / sandbox utilities / packages & APIs） | 行 729 |
| 12 | 10 或 20 个 | Manus 当前原子函数数量 | 行 1369、行 1373 |
| 13 | 30 个 | 单次放进上下文的工具数经验上限（自认「随机数字」） | 行 1369 |
| 14 | 5 次 | Manus 重构次数 | 行 1065 |
| 15 | 3 月 | Manus 发布月份 | 行 1065 |
| 16 | 10 月 | 演讲时点（「现在已经是十月」） | 行 1065 |
| 17 | 1–2 个月 | Manus 做一次架构复审的周期 | 行 1113 |
| 18 | 6–7 个月 | 自 Manus 发布至今的时间跨度 | 行 857 |
| 19 | 3 个阶段 | Open Deep Research 的流程阶段数（scoping / 多智能体研究 / 一次性写作） | 行 281 |
| 20 | Top 10 | Open Deep Research 在深度研究基准上的排名 | 行 281 |
| 21 | 1 个月前 | Wide Research 功能发布时间 | 行 1273 |
| 22 | 10 个 | 举例中主 agent 可生成的子 agent 数量（示例，非上限） | 行 1273 |
| 23 | 1/3 | 3–4 月日志中「更新 todo 列表」占全部动作的比例 | 行 1513 |
| 24 | 3 月或 4 月 | 上述日志样本的时间 | 行 1513 |
| 25 | 1–5 星 | 用户对每个完成会话的评分制 | 行 1737 |
| 26 | 3 类 | 现行评测体系（用户星级 / 内部自动化 / 人类实习生） | 行 1737 |
| 27 | 7 月 | 上一篇 Context Engineering 博客的发布时间 | 行 361 |
| 28 | 5 个 | 上下文工程维度（offload/reduce/retrieve/isolate/cache） | 行 825 |
| 29 | 免费套餐 | 结尾提到的 Manus free tier | 行 1865 |
| 30 | 3 | 复杂搜索的子 agent 输出 schema 由主 agent 定义（对应 1 个 Submit Result 工具） | 行 1273 |

**文中未给出**：任何具体价格/单价、cache hit rate、cache 折扣倍率、明确的时间预算（time budget）、明确延迟数值、并发数、上下文窗口的模型名级规格。行 1321 只做「旗舰模型有时比开源更便宜」的定性判断。

---

## 3. 迭代 / 演进

1. **动作空间：静态 → 无限可扩展。** 行 489：MCP 的发布「完全改变了 Manus 的设计，从一个紧凑的静态动作空间，变成某种无限可扩展的东西」。
2. **规划：todo.md 范式 → 结构化 planner agent。** 行 1509、行 1513：早期用 todo.md，约 3–4 月时约 1/3 的动作是更新 todo 列表、浪费大量 token；现在改为结构化规划，planner 用 agent-as-tool 实现；**最新版本已不再使用 todo.md**（但承认 todo.md 仍能出好结果）。
3. **执行范式：全 CodeAct → 混合。** 行 1433：曾尝试让 Manus「完全使用 CodeAct」，因代码无法 constrained decoding 而放弃；行 1465：改为混合，CodeAct 只用于大数据量等特殊场景。
4. **工具接入：注入 function calling → 沙箱 CLI。** 行 777：不把 MCP 工具注入 function calling 空间，全部走沙箱内的 Manus MCP CLI。
5. **工具规模：多工具/动态加载 → 固定少量原子函数 + 三层卸载。** 行 745、行 1369、行 1373：当前只有 10–20 个原子函数，其余在沙箱；明确不动态拉取工具。
6. **架构整体重构 5 次。** 行 1065：3 月发布、10 月时已重构 5 次。
7. **评测：GAIA 等公共学术基准 → 三类评测。** 行 1737：上线后发现公共基准与用户喜好严重不对齐，现在以用户星级 + 内部可验证自动化 + 人类实习生为主。
8. **训练路线：自训模型/微调/RL → 放弃。** 行 425（上一家公司自训的教训）、行 441（微调陷阱）、行 1769/行 1785（不做 RL，改无参数在线学习）。
9. **模型策略演进：** 行 1321：当前不用任何开源模型（成本/KV cache 原因）；行 1337：优先 Anthropic 但不绑定单一模型，做任务级/子任务级路由。
10. **缓存管理：** 行 1353：依赖提供商能力（如 Anthropic 的 prompt caching）——由 Lance 点明，Peak 确认未展开。
11. **护栏：** 行 1705：渐进式——当前让用户更频繁接管，模型自身护栏变好后可减少人工确认。
12. **记忆：** 行 1033：从「knowledge 显式记忆 + 用户确认」向更自动的集体反馈/无参数在线学习探索（进行中，非已上线）。
13. **架构迭代方法论。** 行 1065、行 1097：不关心静态基准上的静态性能，而是**固定 agent 架构、在不同模型间切换**；若架构能从弱模型换强模型获益，说明更面向未来（因为明天的弱模型可能等于今天的强模型）；行 1113：每 1–2 个月做一次复审，内部用开源模型与提前访问的专有模型，为下一版做准备，甚至早于新模型发布。

---

## 4. 对比（Manus 的做法 vs「常规做法」）

| 维度 | 常规做法 / 其他框架 | Manus 的做法 | 锚点 |
|---|---|---|---|
| 上下文卸载 | 把工具输出全量留在消息历史 | 落盘到文件系统，只回传最小必要信息 | 行 169 |
| 上下文缩减 | 剪除/总结工具调用（如 Open Deep Research、Claude 4.5 内置） | 区分可逆 compaction 与不可逆 summarization，先 compaction 后 summarization | 行 185、行 201、行 217、行 521 |
| 检索 | 索引 + 语义搜索（Cursor） | 无索引库，只用 grep/glob（同 Claude Code）；例外是长期记忆/企业知识库 | 行 233、行 1001 |
| 多智能体 | 按角色划分（designer / manager / programming agent，拟人化组织架构） | 不按角色划分；仅 general executor + planner + knowledge management（+ data API registration） | 行 1577、行 1593 |
| 子智能体通信 | 泛化的多 agent 互传（Cognition/Walden 指出的 Devin 痛点） | by communicating vs by sharing memory 二分；重点用 schema + Submit Result 约束输出 | 行 617、行 1257、行 1273 |
| 工具加载 | 对工具描述做动态 RAG / 语义相似度按需取工具 | 拒绝；改为三层分层动作空间（固定原子函数 + 沙箱 CLI + 代码/API） | 行 713、行 1369 |
| 工具命名 | 复用 Claude Code 同名工具以蹭其后训练（Lance 提问） | 故意不同名，避免模型被不同入参语义困惑 | 行 1801、行 1817 |
| 执行范式 | 纯 CodeAct（每步生成脚本执行） | 混合：直接 function call + 沙箱 CodeAct 并存 | 行 1417、行 1433、行 1465 |
| 数据格式 | Markdown 优先 | 行式纯文本优先，因为 markdown 让模型输出过多 bullet | 行 1145 |
| 总结提示 | 自由形式提示让模型自由生成 | 结构化 schema 表单式填写 | 行 1177 |
| 模型选择 | 单模型绑定 / 用开源模型降本 | 不用开源模型（成本反而更高）；多模型任务级路由 | 行 1321、行 1337 |
| 训练策略 | 自训基座 / 微调 / RLVR（如 Anthropic 在自家 harness 上做 RL） | 不训练、不做 RL；押注通用模型 + 上下文工程 + 无参数在线学习 | 行 425、行 505、行 1769、行 1785 |
| 评测 | 公共学术基准（GAIA 等） | 用户星级 + 可验证自动化 + 人类实习生评审 | 行 1737 |
| 规划 | 直接让主 agent 维护 todo 列表 | 独立 planner agent（agent-as-tool），可用不同模型 | 行 1509、行 1513、行 1545 |
| 哲学 | 增加上下文管理层与检索技巧 | 「build less and understand more」——简化才是最大跃升来源 | 行 841、行 857、行 873 |

---

## 5. 直接引用（原文英文逐字，≤25 词）

1. 行 121 — “typical tasks require around 50 tool calls.”
2. 行 169 — “you don't need all context to live in this messages history of your agent”
3. 行 425 — “Our product's innovation speed was completely capped by the model's iteration speed.”
4. 行 489 — “it completely changed the design of Manus from a compact static action space to something like it's infinitely extensible”
5. 行 521 — “context engineering is the clearest and most practical boundary between application and model”
6. 行 537 — “in the compact format, we can safely drop the super long content field and just keep the path”
7. 行 569 — “compaction is reversible, but summarization isn't”
8. 行 569 — “most models start degrading much earlier, typically maybe around 200k”
9. 行 585 — “we might compact like the oldest 50% of tool calls while keeping the newer ones in full detail”
10. 行 601 — “we still like keep the last few tool calls, the tool results in full detail, not summary”
11. 行 617 — “Do not communicate by sharing memory. Instead, share memory by communicating.”
12. 行 681 — “since the system prompt and the action space differs, you cannot reuse the KV cache”
13. 行 713 — “we're experimenting with a new layered action space in Manus”
14. 行 777 — “We do not inject MCP tools into the function calling space.”
15. 行 857 — “Every time we simplified the architecture, the system got faster, more stable and smarter.”
16. 行 873 — “build less and understand more”
17. 行 1065 — “we have already refactored Manus for five times”
18. 行 1273 — “you have to let the main agent to define the output schema”
19. 行 1321 — “using like these flagship models can sometimes can, they can be even more cheaper than like using open source models”
20. 行 1369 — “try not to like include more than like 30 tools”
21. 行 1513 — “the latest version is we're no longer using that to\-do\.md thing”
22. 行 1513 — “maybe like 1/3 of the action is like updating the to\-do list and waste a lot of like, like tokens”
23. 行 1737 — “we have to use a lot of real human interns to do like, like evaluations”

---

## 6. 听写存疑

以下为机器转录中的乱码、歧义或我做了推断之处。**除注明外，均未据推测补全内容。**

1. **行 7 vs 行 1893（时长矛盾）**：文件头写「1h 52s」（任务描述称 1h52m），但最后一条时间戳是 01:00:43，且行 1833 已说「我们到时间了」。转录很可能**只覆盖约 1 小时**，或时间戳存在错标。无法从文本判定。
2. **行 105**：“context engineering emerged, can it this year around May” 语法破碎，疑为 “context engineering emerged… around May this year”。
3. **行 249**：「Manus Wide Agents」疑为 **Wide Research** 的误听（行 1273 明确叫 Wide Research），但我未在文中找到二者等同的直接陈述，故仅在 1.7 用 Wide Research。同句 “some agents lack for separation of concerns” 语义不通，疑为 “some agents lack separation of concerns”，无法确认。
4. **行 377**：“we'll be focusing on the discourage column” —— 疑指 Lance 幻灯片里的「不推荐/不建议」栏；该栏具体内容转录中未给出，我没有推断其条目。
5. **行 441**：“to make a real work really” 与行 457 “A real work really” 均破碎，疑为 “to make it really work”。
6. **行 569 与行 601 的矛盾**：行 569 明确说腐化阈值「通常是 128k 到 200k」，但 200k 同时也是「开始退化」的估计点，二者关系（阈值下限 vs 上限）文中未进一步澄清。
7. **行 601**：“the gain is tiny because like even if compacted, so use context” 语句不完整，疑为 “even if compacted, it still uses context”。我按此义转写为「即使压缩了，仍然占用上下文」，属推断。
8. **行 617**：引语被归于 “this gopher”，实际出处未点名；引语本身在 Go 社区常归于 Rob Pike。文中未说明出处，我未补充。
9. **行 713**：“So it few\-shots the model into like calling invalid tools” 语法破碎，义为「few-shot 诱导模型调用无效工具」，属合理还原。
10. **行 733**：“Tools too many may cause confusion” 重复啰嗦，与行 729 同义。
11. **行 793**：“a paper like from one of my friend called CodeAct” —— CodeAct 的作者关系文中未说明，我未核实、未断言。
12. **行 969 vs 行 973**：英文说工具位于 “a specific folder”，中文行 973 直接写成 `/usr/bin`。`/usr/bin` 更像是类比而非 Manus 实际路径，存在中译自行具体化的风险。
13. **行 1033**：“there will be some arrows in those rendered visualizations” —— 「arrows」很可能误听；结合上下文（CJK 字体缺失）更像是缺字方框/tofu。我按「字体问题导致渲染异常」整体处理，未确定具体现象。
14. **行 1145**：“for some model, I don't wanna say that name, but they often like. I'll put too many bullet points” —— 语法破碎，且说话人刻意不点名该模型；我未猜测是哪个模型。
15. **行 1273**：“we call it Agentic Map Reduce” / 「Agentic MapReduce」拼写不统一；“maybe 10 sub agent” 是举例，**不能**读作并发上限。
16. **行 1337**：“if you can like plurate that kind of KV cache validation” —— 「plurate」是明显误听，疑为 “prefill” 或 “reuse/validate”。我按「处理 KV cache 复用」处理，属推断。
17. **行 1369**：30 个工具的上限被说话人自己称为 “just a random number in my mind”，不应作为硬性规格引用。
18. **行 1513**：「1/3 的操作是更新 todo 列表」基于 3–4 月的日志抽查，说话人未给出样本量或统计方法，属轶事性数据。
19. **行 1641 / 行 1657**：Lance 的插话 “We have put a lot” 与 Peak 的话被转录切成两段，说话人归属可能错位（行 1657 被标为 Speaker 1，但语义上像是 Speaker 2 的下半句）。我按语义归给 Peak，属推断。
20. **行 1737**：“You know, we have to use a lot of real human interns” 中 “interns” 也可能是 “annotators/raters” 的误听，但多处出现且一致，暂按「实习生」处理。
21. **多处专有名词大小写/拼写**：Thinking Machine Team（应为 Thinking Machines）、Tinker API、GAIA、Noto/CJK、Grok、Manus MCP CLI、Submit Result、advanced search —— 拼写以转录为准，未做规范化。
22. **未出现的数字**：价格、cache hit rate、并发、延迟、显式 time budget 在全文中均未出现；若其他文档引用了 Manus 的这些数字，**不能**追溯到此转录。
