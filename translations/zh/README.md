**赶时间？** [单页速查表](../../CHEATSHEET.md) 涵盖所有快捷键、命令和常用模式。

## 目录

各章节目前仍为英文 — 位于 [`docs/`](../../docs/) 目录。欢迎提交中文章节翻译的 PR：[翻译指南](../README.md)。

| # | 章节 | 你将学到的内容 |
|---|------|----------------|
| 1 | [Getting Started](../../docs/01-getting-started.md) | 安装、登录、第一次会话、权限模式 |
| 2 | [Core Concepts](../../docs/02-core-concepts.md) | Agent 循环、工具、context window — 它究竟如何工作 |
| 3 | [Everyday Workflows](../../docs/03-everyday-workflows.md) | 调试、重构、测试、Git、代码审查 |
| 4 | [CLAUDE.md & Memory](../../docs/04-context-claude-md-memory.md) | 让 Claude 记住你的项目和偏好 |
| 5 | [Slash Commands & Skills](../../docs/05-slash-commands-and-skills.md) | 内置命令与自定义 skill |
| 6 | [Hooks](../../docs/06-hooks.md) | 围绕 Agent 行为的确定性自动化 |
| 7 | [MCP & Integrations](../../docs/07-mcp-integrations.md) | 该接哪些服务器、为什么接 |
| 8 | [Obsidian Workflows](../../docs/08-obsidian-workflows.md) | 将知识库变成 Claude 可读写的信息源 |
| 9 | [Automation & Headless](../../docs/09-automation-and-headless.md) | CI、GitHub Actions、定时任务、脚本化 |
| 10 | [Tips, Tricks & Cost](../../docs/10-tips-tricks-cost.md) | 省时省钱的高效使用习惯 |
| 11 | [Security Best Practices](../../docs/11-security-best-practices.md) | 密钥管理、权限控制、安全使用 |
| 12 | [Agents & Delegation](../../docs/12-agents-and-delegation.md) | Subagent、角色分工、像技术负责人一样工作 |
| 13 | [Token Efficiency](../../docs/13-token-efficiency.md) | 用更少 token 完成更多工作 |

## 社区 Recipe

[`recipes/`](../../recipes/) 收录了来自真实用户的简短工作流 — 这也是参与贡献最简单的方式：**一个文件、一个 recipe、一个 PR**（[模板在此](../../recipes/_template.md)）。

## 开箱即用的示例

[`examples/`](../../examples/) 目录中的文件可直接复制到你的项目中：

- [`CLAUDE.md.example`](../../examples/CLAUDE.md.example) — 结构完善的项目记忆文件
- [`settings.json.example`](../../examples/settings.json.example) — 合理的权限和 hook 配置
- [`hooks/`](../../examples/hooks/) — 编辑时自动格式化、命令日志、受保护文件守卫
- [`skills/`](../../examples/skills/) — 自定义 `/changelog` skill
- [`agents/`](../../examples/agents/) — 代码审查 subagent 定义

## 这份指南适合谁？

- **新手** — 第 1–3 章带你从零到高效。
- **日常用户** — 第 4–7 章是真正的杠杆所在。
- **喜欢折腾的人** — 第 8–9 章教你把 Claude Code 融入整个系统，而不只是编辑器。
- **高级用户** — 第 12–13 章：管理一支 Agent 团队，且不烧光你的用量限制。

## 参与贡献

发现了错误？有值得分享的工作流？欢迎提交 PR — 详见 [CONTRIBUTING.md](../../CONTRIBUTING.md)。中文章节翻译同样是非常受欢迎的贡献。

## 许可证

[MIT](../../LICENSE) — 自由使用。

本指南为**非官方社区文档**，与 Anthropic 无关联，亦未获其背书。*Claude* 和 *Claude Code* 是 Anthropic, PBC 的商标。权威参考请见 [官方文档](https://code.claude.com/docs)。