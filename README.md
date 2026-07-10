# How It Works: Claude Code 🛠️

**A practical, no-fluff field guide to Claude Code — the AI coding agent that lives in your terminal.**

🌍 English · [Türkçe](translations/tr/README.md) · [add your language](translations/README.md)

This repo is written for people who want to go beyond "chat with an AI" and actually *work* with one: refactor real codebases, automate boring chores, wire it into your tools, and build workflows that stick.

> Everything here uses placeholder values (`YOUR_API_KEY`, `your-project`, etc.). Never commit real secrets — see [Security](docs/11-security-best-practices.md).

---

## What is Claude Code?

Claude Code is Anthropic's agentic coding tool. Instead of copy-pasting snippets into a chat window, you run `claude` inside your project and it:

- **Reads your codebase** on demand — it searches, opens files, and builds its own understanding.
- **Edits files and runs commands** — with your permission, it writes code, runs tests, fixes what breaks, and iterates.
- **Uses git like a teammate** — commits, branches, PR descriptions, code review.
- **Extends itself** — hooks, skills, subagents, and MCP servers let you connect it to almost anything.

It's available as a terminal CLI, a desktop app, a web app, and IDE extensions (VS Code, JetBrains).

## Quick start (60 seconds)

```bash
# Install (macOS / Linux / WSL — Windows PowerShell: irm https://claude.ai/install.ps1 | iex)
curl -fsSL https://claude.ai/install.sh | bash

# Go to any project and start
cd your-project
claude
```

First run walks you through login (Claude Pro/Max subscription or an API key). Then just talk to it:

```
> explain what this project does
> find where user authentication happens and add rate limiting
> run the tests and fix any failures
```

**In a hurry?** The [one-page cheatsheet](CHEATSHEET.md) has every key, command, and pattern.

## Table of contents

| # | Chapter | What you'll learn |
|---|---------|-------------------|
| 1 | [Getting Started](docs/01-getting-started.md) | Install, auth, first session, permission modes |
| 2 | [Core Concepts](docs/02-core-concepts.md) | The agent loop, tools, context window, how it *actually* works |
| 3 | [Everyday Workflows](docs/03-everyday-workflows.md) | Debugging, refactoring, tests, git, code review |
| 4 | [CLAUDE.md & Memory](docs/04-context-claude-md-memory.md) | Teaching Claude your project and your preferences |
| 5 | [Slash Commands & Skills](docs/05-slash-commands-and-skills.md) | Built-in commands and writing your own |
| 6 | [Hooks](docs/06-hooks.md) | Deterministic automation around Claude's actions |
| 7 | [MCP & Integrations](docs/07-mcp-integrations.md) | Which servers to connect and why |
| 8 | [Obsidian Workflows](docs/08-obsidian-workflows.md) | Turn your vault into a knowledge base Claude can read and write |
| 9 | [Automation & Headless Mode](docs/09-automation-and-headless.md) | CI, GitHub Actions, cron jobs, scripting |
| 10 | [Tips, Tricks & Cost Control](docs/10-tips-tricks-cost.md) | Power-user habits that save time and money |
| 11 | [Security Best Practices](docs/11-security-best-practices.md) | Secrets, permissions, and staying safe |
| 12 | [Agents & Delegation](docs/12-agents-and-delegation.md) | Subagents, roles, and working like a tech lead |
| 13 | [Token Efficiency](docs/13-token-efficiency.md) | More work per token — for subscriptions and API alike |

## Community recipes

[`recipes/`](recipes/) is a cookbook of short, real workflows from users — and the easiest place to make your first contribution: **one file, one recipe, one PR** ([template here](recipes/_template.md)).

## Ready-to-use examples

The [`examples/`](examples/) folder contains files you can drop straight into your own projects:

- [`CLAUDE.md.example`](examples/CLAUDE.md.example) — a well-structured project memory file
- [`settings.json.example`](examples/settings.json.example) — sane permissions + hooks configuration
- [`hooks/`](examples/hooks/) — auto-format on edit, command logging, protected-file guard
- [`skills/`](examples/skills/) — a custom `/changelog` skill
- [`agents/`](examples/agents/) — a code-reviewer subagent definition

## Who is this for?

- **Newcomers** — chapters 1–3 take you from zero to productive.
- **Daily users** — chapters 4–7 are where the real leverage is.
- **Tinkerers & automators** — chapters 8–9 show how to make Claude Code part of your systems, not just your editor.
- **Power users** — chapters 12–13: run a team of agents, and do it without burning your usage limits.

## Contributing

Found a mistake? Have a workflow worth sharing? PRs are very welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). This guide gets better with real-world recipes from real users.

**Currently looking for** (see [open issues](../../issues) for details):

- Recipes for `recipes/` — any workflow you actually use ([template](recipes/_template.md))
- Translations — Türkçe is started; any language welcome ([how](translations/README.md))
- Chapter re-verification — check a chapter against the current release and bump its "Last verified" banner
- A Windows-specific chapter (native vs WSL gotchas)
- A Plugins chapter (`/plugin`, marketplaces)
- A dedicated Troubleshooting & FAQ chapter
- Corrections — Claude Code moves fast; stale docs reported or fixed are gold

## License & disclaimer

[MIT](LICENSE) — use anything here freely.

This is an **unofficial community guide**, not affiliated with or endorsed by Anthropic. *Claude* and *Claude Code* are trademarks of Anthropic, PBC. For authoritative reference, see the [official documentation](https://code.claude.com/docs).
