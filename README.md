# Manus Agent Research

关于 Manus（manus.im）AI Agent 架构的公开资料整理。

## 内容

| 文件 | 说明 |
| --- | --- |
| [manus-context-engineering.md](manus-context-engineering.md) | Manus 官方博客《AI 代理的上下文工程：构建 Manus 的经验教训》全文，按「问题 / 答案 / 原因 / 例子 / 结论」结构重新整理，含 7 张官方配图 |
| [images/](images/) | 原文配图，取自 Manus 官方 CDN |

## 出处与版权

- 原文：<https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus>
- 作者：季逸超（Yichao "Peak" Ji），Manus 联合创始人、首席科学家
- 原文日期：2025-07-18

本仓库是对上述原文的**结构化整理与重排**，文字与图片著作权归 Manus 所有，仅作学习与研究用途并保留完整署名与出处。如权利人提出异议，将立即移除相关内容。

## 译源说明

整理时互校了两个中文版本：

- Manus 官方中文页（在「遮蔽，而非移除」一节处被截断）
- [智源社区译本](https://hub.baai.ac.cn/view/47452)（Kimi K2 译），第 5–9 节以此为准

⚠️ [虎嗅/搜狐那版](https://www.sohu.com/a/915615125_115207)（GPT 译）把 token 单价误译为「0.30 美元/千标记」，原文与官方中文版均为 **$0.30/百万 token（MTok）**；该版本还漏掉了 Hermes `Required` 模式的预填充片段。**请勿引用那一版的数字。**

## 目录结构

```
.
├── README.md
├── manus-context-engineering.md
└── images/
    ├── 00-cover.webp
    ├── 01-kv-cache.png
    ├── 02-mask.png
    ├── 03-filesystem.png
    ├── 04-recitation.png
    ├── 05-errors.png
    └── 06-few-shot.png
```

## 同系列

本仓库是「一个仓库钻一个系统」系列里的一篇。横向对照（三家放在一起看）在
[multi-agent-architecture-notes](https://github.com/webaifei/multi-agent-architecture-notes)。

| 仓库 | 在线读 | 讲什么 |
| --- | --- | --- |
| dsh-agent-loop-notes | [在线](https://webaifei.github.io/dsh-agent-loop-notes/) · [源码](https://github.com/webaifei/dsh-agent-loop-notes) | DeepSeek Harness 的三层 `while`、两条队列、重试为什么不算新步骤 |
| codex-agent-loop-notes | [在线](https://webaifei.github.io/codex-agent-loop-notes/) · [源码](https://github.com/webaifei/codex-agent-loop-notes) | Codex 的三层 `loop`，加一个叫 `needs_follow_up` 的布尔 |
| muse-architecture-notes | [在线](https://webaifei.github.io/muse-architecture-notes/) · [源码](https://github.com/webaifei/muse-architecture-notes) | Meta Muse 的权限模型、凭据代理、状态机 |
| multi-agent-architecture-notes | [在线](https://webaifei.github.io/multi-agent-architecture-notes/) · [源码](https://github.com/webaifei/multi-agent-architecture-notes) | 三家横向对照：13 张架构图 + 可运行实现 |
| manus-agent-research | [源码](https://github.com/webaifei/manus-agent-research) | Manus 的公开资料整理 |
