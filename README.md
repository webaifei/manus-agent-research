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
