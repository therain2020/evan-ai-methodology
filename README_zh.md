# Evan's AI Methodology

> 不是"怎么装"，是"为什么这么用"。两年踩坑攒下来的 AI 辅助开发方法论。
>
> [English Version](README.md)

[evan-ai-setup](https://github.com/therain2020/evan-ai-setup) 是一步一步的安装配置脚本。这本是配套笔记：每条规则是怎么来的，为什么长成现在这个样子。两本合在一起才是完整的工具链。

## 适合谁

已经在用 Cursor、Claude Code 或 Copilot，发现"快是快了，代码质量没跟上"。

想让 AI 帮你理架构、做 review，而不只是敲代码。里面的内容也许对你有用。

## 章节

| # | 章节 | 一句话 |
|---|------|--------|
| 1 | [构建者心态](01-builders-mindset.md) | AI 把边际成本打到了零——什么变了，什么没变 |
| 2 | [Agent 编排](02-agent-orchestration.md) | 单 agent 还是拆成多个，什么时候该分派 |
| 3 | [退化感知](03-degradation-awareness.md) | 静默降级比直接炸掉更危险，尤其在 AI 系统里 |
| 4 | [跟 AI 一起调试](04-debugging-with-ai.md) | 先找根因再写 fix——AI 怎么改变了调试的节奏 |
| 5 | [论文即代码](05-thesis-as-code.md) | 把毕业论文当成软件工程来管，结构化拆解 + 版本控制 |
| 6 | [Skill 设计](06-skill-design.md) | 什么时候值得写个自定义 skill，什么才算一个好 skill |
| 7 | [Review 流水线](07-review-pipeline.md) | 分层审查，从表面到深层各管一摊 |
| 8 | [MCP 即基础设施](08-mcp-as-infrastructure.md) | 外部工具怎么接入 AI 工作流，选型原则和踩坑记录 |

## 阅读顺序

刚接触从第 1 章开始，接着第 2 章。debug 翻第 4 章，写 skill 看第 6 章，搭 review 关卡第 7 章。按需跳着读，各章独立。

## 这些是什么

不是教科书。是我反复碰壁之后整理的现场笔记。写到一半就发现这个领域变得太快，没法当"权威指南"写。当成工作日志读好了——一个开发者在 2024-2025 年 AI 工具爆炸期，什么时候觉得好用，什么时候踩了大坑。

读完了觉得不对，可能是我搞错了，也可能只是场景不同。欢迎提 issue。

## 配套仓库

[evan-ai-setup](https://github.com/therain2020/evan-ai-setup) — 安装配置，拿来就能用。
