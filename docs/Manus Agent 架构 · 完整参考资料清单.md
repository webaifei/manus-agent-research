# Manus Agent 架构 · 完整参考资料清单



标注：**A** = 可当事实引用 · **B** = 可信但非官方 · **C** = 第三方观点 · **D** = 不可用/需避开



---



## 一、Manus 官方博客（A）



- [Context Engineering for AI Agents: Lessons from Building Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus) — 2025\-07\-18 · **最重要的一篇**：KV\-cache、100:1、遮蔽工具、文件系统当记忆、todo\.md、保留错误

- [中文版](https://manus.im/zh-cn/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)

- [Introducing Wide Research](https://manus.im/en/blog/introducing-wide-research) — 2025\-07\-31 · 并行子 agent、"subagent 是完整通用 Manus 实例"

- [Manus 1\.5 发布](https://manus.im/zh-cn/blog/manus-1.5-release) — 2025\-10\-16 · 重架构引擎、15min→4min

- [Introducing Manus 2\.0](https://manus.im/blog/introducing-manus-2-0) — 2026\-09\-28 · **Cascade**、Cloud Computer、Automations、Cue

- [Projects Just Got Smarter with Connectors](https://manus.im/en/blog/projects-connectors) — 2025\-12\-19 · Connectors 机制（就是那个 GitHub App 的来路）

- [ManusAI Joins Meta](https://manus.im/blog/manus-joins-meta-for-next-era-of-innovation) — 2025\-12\-29

- [Manus AI 100M USD ARR](https://manus.im/blog/manus-100m-arr) — 2025\-12\-28

- [A Note to Our Users](https://manus.im/blog/a-note-to-our-users) — 2026\-08\-11 · 与 Meta 分离的官方说明 \+ FAQ

- [Manus: My Computer](https://manus.im/blog/manus-my-computer-desktop) — 2026\-03\-16 · 本地桌面 agent

- [Agents 功能页](https://manus.im/features/agent-skills) · 三级渐进披露的具体数字（元数据 \~100 token / 指令 \<5k / 资源按需）

    

## 二、Manus 官方文档（A）



- [文档首页](https://manus.im/docs/introduction/welcome)

- [Wide Research](https://manus.im/docs/features/wide-research) · 四步架构 \+ "fabrication threshold 8–10 项"

- [Manus Skills](https://manus.im/docs/features/skills) · **SKILL\.md**、三级 Progressive Disclosure

- [Projects](https://manus.im/docs/features/projects) · [Automations](https://manus.im/docs/automations)

- [Help Center](https://help.manus.im/en) · [Trust Center](https://trust.manus.im/)

    

## 三、Manus API（A · 这次最被低估的一手材料）



- [API v2 介绍](https://open.manus.ai/docs/v2/introduction) · [**OpenAPI spec**](https://open.manus.ai/docs/v2/openapi_v2.json) · [v1 旧文档](https://open.manus.ai/docs/v1/overview)

- [Task Lifecycle](https://open.manus.ai/docs/v2/task-lifecycle) · **事件词表**（`tool_used` / `plan_update` / `new_plan_step` / `explanation`）、`has_running_background_jobs`

- [Agents](https://open.manus.ai/docs/v2/agents-overview) · agent = 带持久 main task 的实体

- [Connectors](https://open.manus.ai/docs/v2/connectors) · [Artifacts](https://open.manus.ai/docs/v2/artifacts)

- [Hidden application context](https://open.manus.ai/docs/v2/hidden-application-context) · 隐藏文本注入通道

- [Open App](https://open.manus.ai/docs/v2/open-app) · `team` / `trusted_team` / `trusted_public` 三种客户端

- [Manus API skill](https://open.manus.ai/docs/v2/manus-api-skill) · [Structured Output](https://open.manus.ai/docs/v2/structured-output)

    

## 四、官方 / 员工发言（A）



- [PingCAP × Manus 案例研究](https://www.pingcap.com/case-study/manus-agentic-ai-database-tidb/) — \~2025\-11 · **工程 VP 苗子明** 主讲：**Action Engine = Plan → Execute → Verify**、最多 15,000 次迭代、TiDB 当长期记忆

- [极客公园：季逸超 Stripe Tour 对谈全文](https://w.geekpark.net/news/352981) — 2025\-08\-22 · "工具的网络效应"、看图自检

- [LangChain × Manus webinar 中文实录](https://www.vergex.cn/3511.html) — 压缩 vs 摘要、通信/共享内存两种隔离、三层行为空间

    - 原视频 [YouTube](https://www.youtube.com/watch?v=6_BcCthVvb8) · [B站 BV1pEWizJEwY](https://www.bilibili.com/video/BV1pEWizJEwY)

    - 微信原文 `mp.weixin.qq.com/s/lAl-hZGy7i-b53m5SLFyWA`

- [即刻 hidecloud（张涛）转推](https://m.okjike.com/originalPosts/68f0ee974be4e5a50aa18617)

    

### X / Twitter（x\.com 抓不到，靠存档站）



- `x.com/peakji/status/1898994802194346408`（2025\-03\-12 回应越狱）

- `x.com/peakji/status/1898225941954142430`（否认操控手机视频）

- `x.com/peakji/status/1950953265669214694`（2025\-08\-02 Wide Research，**homogeneous agents**）

- `x.com/peakji/status/2023698858287730870`（2026\-02\-17 澄清非 OpenClaw，四项设计）

- `x.com/jianxliao/status/1898861051183349870`（泄露者原帖）· `x.com/op7418/status/1898938027063558154`

- `x.com/ManusAI`（官方号）

- 存档站：[texxr\.com/handle/peakji](https://texxr.com/handle/peakji) · [sotwe\.com/peakji](https://www.sotwe.com/peakji)

    

## 五、泄露 prompt 与逆向代码（B）



- [gist jlia0](https://gist.github.com/jlia0/db0a9695b3ca7609c9b1a08dcbf872c9) — **原始泄露**，2\.6k star

- [jujumilk3/leaked\-system\-prompts](https://github.com/jujumilk3/leaked-system-prompts) · [raw 0309](https://raw.githubusercontent.com/jujumilk3/leaked-system-prompts/main/manus_20250309.md) · [raw 0310](https://raw.githubusercontent.com/jujumilk3/leaked-system-prompts/main/manus_20250310.md) — **29 工具全 schema \+ planner/knowledge/datasource 三模块**

- [x1xhlol/system\-prompts\-and\-models\-of\-ai\-tools](https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools) — V3 镜像，英文世界主要出处

- [elder\-plinius/CL4R1T4S](https://github.com/elder-plinius/CL4R1T4S) · [Manus\_Functions\.txt](https://raw.githubusercontent.com/elder-plinius/CL4R1T4S/main/MANUS/Manus_Functions.txt) — **V4（2025\-04）27 工具**，⚠️ recitation，方向可信字面不可引

- [Continuum\-AI\-Corp/OrcaPromptVault](https://github.com/Continuum-AI-Corp/OrcaPromptVault) — 明确把 Manus 归为 "testimony" 而非 captured

- ⭐️⭐️⭐️[whit3rabbit/manus\-open](https://github.com/whit3rabbit/manus-open) — **真实沙箱侧逆向**：FastAPI/8330、apiproxy、openapi\.yaml、browser\_use 改造点

    

## 六、沙箱与基础设施（B）

- [E2B 客户案例](https://e2b.dev/customers/how-manus-uses-e2b-to-provide-agents-with-virtual-computers) — 2025\-05\-06 · Firecracker microVM、150ms、弃用 Docker

- [AWS 官方新闻稿](https://press.aboutamazon.com/aws/2025/12/manus-selects-aws-to-power-general-purpose-ai-agent-serving-millions-globally) — 2025\-12

- [DigitalToday 韩媒转述](https://www.digitaltoday.co.kr/news/articleView.html?idxno=615532) — 2025\-12\-23 · Bedrock、"3 人管数万沙盒"

    

## 七、安全研究（A · 最有架构价值）



- [Salt Labs：How We Hijacked an AI Agent With a Single Email](https://salt.security/blog/how-we-hijacked-an-ai-agent-with-a-single-email) — 2026\-10\-01 · JSFuck 绕过、**guardrail 检测晚于执行**、凭证在沙箱内

    - 后续篇 [When a Security Guardrail Detects the Attack and Still Can't Stop It](https://salt.security/blog/when-a-security-guardrail-detects-the-attack-and-still-cant-stop-it)

- [Aurascape AuraLabs：SilentBridge](https://aurascape.ai/resources/auralabs-research/silentbridge-zero-click-agent-takeover-meta-manus/) — 2026\-02\-24 · CVSS 9\.8、免密 sudo → root、滥用 `deploy_expose_port`、CDN 跨租户

    

## 八、学术论文



- **A** [arXiv:2509\.14528](https://arxiv.org/abs/2509.14528) — CMU，N=31 可用性研究，**ACM CAIS 2026 接收**（唯一同行评审的 Manus 实证）

- **D** [arXiv:2505\.02024](https://arxiv.org/abs/2505.02024) — ⚠️ 推测性综述，编造 RLHF，**不要引用**

- [arXiv:2604\.15715](https://arxiv.org/abs/2604.15715) — GTA\-2，把 execution harness 列为评测对象

- [arXiv:2606\.24112](https://arxiv.org/abs/2606.24112) — ReMMD，正文明确评测 Manus

    

## 九、第三方工程分析（B/C）



- [philschmid：Context Engineering Part 2](https://www.philschmid.de/context-engineering-part-2) — 2025\-12\-04 · **对研讨会最好的英文整理**

- [ZenML LLMOps Database](https://www.zenml.io/llmops-database/context-engineering-for-production-ai-agents-at-scale) — 五维框架 \+ 三层 action space（⚠️ 人名/日期有错）

- [Lance Martin @ Latent Space](https://podscan.fm/podcasts/latent-space-the-ai-engineer-podcast/episodes/context-engineering-for-agents-lance-martin-langchain)

- [FastCampus 韩文 webinar 笔记](https://fastcampus.co.kr/community/102926) — 2026\-01\-10 · 128K–200K 阈值

- [FutureAGI](https://futureagi.com/blog/manus-ai-comparison-2025/) · [Techsy](https://techsy.io/en/blog/openhands-vs-devin-vs-manus) · [AgentMarketCap](https://agentmarketcap.ai/blog/2026/04/11/computer-use-agent-platform-wars-2026)

    

## 十、媒体与中文报道（C）



- [the\-decoder](https://the-decoder.de/china-ki-agent-manus-ai-nutzt-claude-sonnet-und-open-source-technologie/) — 2025\-03\-10 · 架构确认 \+ 模型栈

- [机器之心（腾讯云）](https://cloud.tencent.com.cn/developer/article/2503845) · [网易转载](https://www.163.com/dy/article/JQAFSDOB0511AQHO.html) — 泄露全过程 \+ 季逸超回应

- [第一财经](https://www.yicai.com/news/102504492.html) — 2025\-03\-10 · 基座模型披露

- [机器之心/澎湃：100\+ 智能体并发](https://m.thepaper.cn/newsDetail_forward_31278253) — 2025\-08\-01 · **含对 Wide Research 的质疑**

- [36氪"首次复盘"](https://36kr.com/p/3385465574496646) · [搜狐/虎嗅镜像](https://www.sohu.com/a/915615125_115207) — ⚠️ 正文其实是官方博客的翻译

- [C114/凤凰网：Manus 重新独立](https://www.c114.net.cn/industry/130260.html) — Cascade 定位 \+ Cloud Computer 上线时间

- [脑极体：套壳起家、20亿收场](https://zhengguannews.cn/zghnews/416610.html) — 批判视角 \+ 成本数据

- [掘金：拆解 Manus 使用文件系统作为上下文](https://juejin.cn/post/7593515253972998) — 三文件逆向

- [腾讯云开发者社区"五层架构"](https://cloud.tencent.cn/developer/article/2672142) — **D，AI 生成的假说**

- 中文译本：[InfoQ 网易号](https://www.163.com/dy/article/K55OBCTB0511D3QS.html) · [智源社区（最准）](https://hub.baai.ac.cn/view/47452)

- [Bloomberg](https://www.bloomberg.com/news/articles/2026-04-27/china-blocks-meta-s-2-billion-acquisition-of-ai-startup-manus) · [CNN](https://www.cnn.com/2026/04/27/tech/china-blocks-meta-manus-intl-hnk) · [CNBC](https://www.cnbc.com/2026/04/27/meta-manus-china-blocks-acquisition-ai-startup.html) · [36氪英文](https://eu.36kr.com/en/p/3785686205930759) · [Eyerys](https://www.eyerys.com/articles/news/manus-starts-rolling-out-manus-20-and-cue-giving-agents-their-own-email-phone-and-wallet)

- [HBR 案例 626052](https://store.hbr.org/product/manus-ai-the-butterfly-effect-technology-a/626052) — Iansiti 等，付费墙

    

## 十一、社区



**Hacker News**（`news.ycombinator.com/item?id=`）



|ID|标题|日期 / 分数|
|---|---|---|
|[43266666](https://news.ycombinator.com/item?id=43266666)|Manus 上线|2025\-03\-05|
|[43298408](https://news.ycombinator.com/item?id=43298408)|Leave It to Manus|2025\-03\-08 / 51|
|[43307713](https://news.ycombinator.com/item?id=43307713)|The political economy of Manus AI|2025\-03\-09 · "只是 Claude wrapper"|
|[43312723](https://news.ycombinator.com/item?id=43312723)|Anus（让 Manus 自己造替代品）|2025\-03\-09 / 18|
|[43434171](https://news.ycombinator.com/item?id=43434171)|LangManus|2025\-03\-21 / 127|
|[43562103](https://news.ycombinator.com/item?id=43562103)|Devin Roasts Manus|2025\-04\-02|
|[45352901](https://news.ycombinator.com/item?id=45352901)|Context Engineering 博客|2025\-09\-23 / 120|
|[46409245](https://news.ycombinator.com/item?id=46409245)|Manus AI 100M ARR|2025\-12\-28|
|[46426534](https://news.ycombinator.com/item?id=46426534)|ManusAI Joins Meta|2025\-12\-29 / **319**|
|[47920315](https://news.ycombinator.com/item?id=47920315)|China blocks Meta's acquisition|2026\-04\-27 / **402**|
|[49258764](https://news.ycombinator.com/item?id=49258764)|回归独立运营|2026\-08\-11 / 169|
|[49881088](https://news.ycombinator.com/item?id=49881088)|Manus 2\.0|2026\-09\-28|



检索用 Algolia API：`https://hn.algolia.com/api/v1/search?query=manus.im&tags=story`



**Reddit**



- [Morro Hsu（前 Manus 后端负责人）r/LocalLLaMA 长文](https://www.reddit.com/r/LocalLLaMA/comments/1rrisqn/i_was_backend_lead_at_manus_after_building_agents/) · [镜像](https://postshift.com/i-was-backend-lead-at-manus-after-building-agents-for-2-years-i-stopped-using-function-calling-entirely-heres-what-i-use-instead-r-localllama-2/) — ⚠️ 讲的是他**离职后**个人项目的做法

- [Ahmad Othman r/ClaudeAI 逆向帖](https://www.reddit.com/r/ClaudeAI/comments/1q2p03x/i_reverseengineered_the_workflow_that_made_manus/) · 项目 [planning\-with\-files](https://github.com/OthmanAdi/planning-with-files) · [npm reference\.md](https://cdn.jsdelivr.net/npm/planning-with-files@3.20.7/reference.md) — 三文件模式，**社区逆向非官方**

    

## 十二、复刻项目与清单



- [FoundationAgents/OpenManus](https://github.com/FoundationAgents/OpenManus) · [旧仓存档](https://github.com/mannaandpoem/OpenManus_Archive) — ⚠️ **核对过：没有抄泄露 prompt**，是原创实现

- [camel\-ai/owl](https://github.com/camel-ai/owl) · [langmanus/langmanus](https://github.com/langmanus/langmanus) · [MaoTouHU/byte\-langmanus](https://github.com/MaoTouHU/byte-langmanus) — ⚠️ 均**非** Manus 架构证据

- [kyopark2014/agent\-manus](https://github.com/kyopark2014/agent-manus) — 第三方复刻品

- [redreamality/awesome\-manus](https://github.com/redreamality/awesome-manus) — **D**，大量占位链接

- [awesome\.manus\.space](https://awesome.manus.space/?category=skills) · [glama\.ai Manus\-MCP](https://glama.ai/mcp/servers/0-SOFT/Manus-MCP)

    

## 十三、拿不到的（省你时间）



|目标|状态|
|---|---|
|`x.com` 原生推文|需登录；Wayback 抓到的是空页（JS 渲染）|
|Nitter / xcancel|**HTTP 451，服务已停服**|
|Reddit `.json` API|**403**|
|知乎|**全部 403**（仅确认标题存在）|
|36kr\.com 原站|反爬"安全检测"页|
|HBR 案例、Bloomberg、Business Times|付费墙|
|`manus.im/blog/manus-wide-research`|404（我猜错的 URL，正确是 `/en/blog/introducing-wide-research`）|
|CVE|**不存在**，两个安全发现都走 bug bounty|
|Manus GAIA verified 条目|**不存在**，全是自报|
|Cascade 独立验证|**不存在**|



---



**如果你的时间有限，按这个顺序读**：① Context Engineering 博客 → ② PingCAP 案例研究 → ③ Salt Labs 安全研究 → ④ 泄露的 `manus_20250310.md` → ⑤ OpenAPI spec。这五份覆盖了 80% 的硬信息，其余都是佐证或评述。



