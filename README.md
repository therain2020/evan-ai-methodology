# Evan's AI Methodology

> 不是"怎么装"，是"为什么这么用"——AI 辅助开发这两年踩过的坑、想明白的事、留下来的一套方法论。
>
> Not a setup guide. A design log — the reasoning behind every rule, every module, every trade-off I made while building my AI-assisted dev workflow.

[evan-ai-setup](https://github.com/therain2020/evan-ai-setup) 是一步一步的安装配置脚本。这本是配套笔记：每条规则是怎么来的，为什么长成现在这个样子。两本合在一起才是完整的工具链。

[evan-ai-setup](https://github.com/therain2020/evan-ai-setup) has the install scripts. This repo has the design notes. Read both if you want the full picture.

## 适合谁 / Who This Is For

已经在用 Cursor / Claude Code / Copilot 写代码，但觉得"快是快了，代码质量没跟上"的开发者。

如果你想让 AI 不只是帮你敲代码，而是帮你理架构、做 review、协调多个 agent 并行干活——这里面的东西可能对你有用。

If you've been using AI coding tools for a while and hit the wall where "faster" stopped meaning "better" — this might help. It's about going from AI-as-typist to AI-as-thinking-partner.

## 章节 / Chapters

| # | 章节 | 一句话 |
|---|------|--------|
| 1 | 构建者心态 / Builder's Mindset | AI 把边际成本打到了零——什么变了，什么没变 |
| 2 | Agent 编排 / Agent Orchestration | 什么时候用一个 agent，什么时候拆成十个，怎么协调 |
| 3 | 退化感知 / Degradation Awareness | 静默降级比直接炸掉更危险，尤其在 AI 系统里 |
| 4 | 跟 AI 一起调试 / Debugging with AI | 先找根因再写 fix——AI 怎么改变了调试的节奏 |
| 5 | 论文即代码 / Thesis as Code | 把毕业论文当成软件工程来管，结构化拆解 + 版本控制 |
| 6 | Skill 设计 / Skill Design | 什么时候值得写个自定义 skill，什么才算一个好 skill |
| 7 | Review 流水线 / Review Pipeline | 分层审查：表层代码、中层逻辑、深层架构，各看各的 |
| 8 | MCP 即基础设施 / MCP as Infrastructure | 外部工具怎么接入 AI 工作流，选型原则和踩坑记录 |

## 阅读顺序 / Reading Order

刚接触 AI 辅助开发：从第 1 章开始，然后第 2 章。正在 debug：第 4 章。写 skill：第 6 章。给团队搭 review 关卡：第 7 章。各章之间没有强依赖，按需跳着读。

New to this: start with Chapter 1, then 2. Debugging: jump to 4. Building a skill: Chapter 6. Setting up review gates for a team: Chapter 7. No hard dependencies between chapters — skip around.

## 关于这些内容 / A Note on What This Is

这些不是标准教科书。是我自己在 AI 辅助开发里反复碰壁之后整理的东西。写到一半我就发现这个领域变化太快了，没法写一本"权威指南"。所以更准确地说，这是一份工作日志——记录了一个开发者在 2024-2025 年 AI 工具大爆发的时候，什么时候觉得"这个真好用"，什么时候觉得"这个坑太大了"，最后是怎么搭自己的工具链的。

如果你读完某章觉得"不太对"，可能是我错了，也可能是我们的使用场景不一样。欢迎提 issue。

These aren't textbook chapters. They're field notes — things I figured out by getting stuck, getting confused, and occasionally getting it right. I stopped trying to write an "authoritative guide" about halfway through, because this space moves too fast for that. Think of it as a work log from 2024-2025, when AI dev tools exploded and everyone was figuring it out in real time.

If something doesn't ring true for you, it might be wrong, or it might just be that our setups are different. Either way, issues are welcome.

## 配套仓库 / Companion Repo

[evan-ai-setup](https://github.com/therain2020/evan-ai-setup) — 工具安装和配置，拿来就能抄。
