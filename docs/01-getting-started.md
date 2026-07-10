# 1. Getting Started

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

## Installation

**Requirements:** macOS, Linux, or Windows (native or WSL). No Node.js needed for the native installer; the npm path needs Node 22+.

```bash
# Native installer — recommended (macOS / Linux / WSL)
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# npm (needs Node 22+)
npm install -g @anthropic-ai/claude-code

# Homebrew (updates via `brew upgrade`, not automatically)
brew install --cask claude-code
```

Verify:

```bash
claude --version
```

The native install keeps itself up to date automatically; Homebrew/npm installs update manually (`brew upgrade claude-code` / `npm install -g @anthropic-ai/claude-code@latest`). `claude update` triggers an update by hand.

## Authentication

Run `claude` in any folder. On first launch you pick one of:

1. **Claude subscription (Pro, Max, Team, or Enterprise)** — log in with your claude.ai account. Usage is included in your plan. This is the simplest option for individuals.
2. **Claude Console / API key** — pay-as-you-go API billing. Good for teams and automation.
3. **Enterprise providers** — Amazon Bedrock or Google Vertex AI, configured via environment variables.

> Which plan? Pro is fine for light daily use. Max (5x / 20x) is for heavy daily driving — big refactors, long sessions, multiple parallel agents. API billing makes sense for CI and scripted use where you want per-token accounting.

## Your first session

```bash
cd your-project
claude
```

Things to try in the first five minutes:

```
> give me an overview of this codebase
> what does the deploy script actually do?
> /init
```

`/init` scans the project and generates a `CLAUDE.md` file — the project's memory. Commit it. (More in [chapter 4](04-context-claude-md-memory.md).)

## The permission model

Claude Code asks before doing anything with side effects. When it wants to edit a file or run a command you'll see a prompt with choices like **Yes**, **Yes, and don't ask again for this command**, and **No, tell Claude what to do differently**.

There are several permission *modes*, switchable with **Shift+Tab** during a session:

| Mode | Behavior | When to use |
|------|----------|-------------|
| **Normal** | Asks for each write/command | Default; unfamiliar codebases |
| **Auto-accept edits** | File edits proceed automatically, commands still ask | Trusted repos, flow state |
| **Plan mode** | Read-only: Claude researches and proposes a plan, touches nothing | Big changes, architecture decisions |
| **Bypass permissions** | No prompts at all (`--dangerously-skip-permissions`) | Only in sandboxes/containers — see [Security](11-security-best-practices.md) |

**Plan mode is underrated.** For any change bigger than a bug fix, press Shift+Tab until you're in plan mode, describe what you want, review the plan, *then* let it execute. You catch misunderstandings before they become 40 edited files.

## Essential keys & commands

| Key / command | What it does |
|---|---|
| `Esc` | Interrupt Claude mid-action (safe — it stops and waits) |
| `Esc Esc` / `/rewind` | Rewind conversation *and/or code* to an earlier checkpoint |
| `Shift+Tab` | Cycle permission modes |
| `Ctrl+C` | Cancel input / exit |
| `!command` | Run a shell command yourself, output lands in the conversation |
| `#some note` | Quickly add a memory to CLAUDE.md |
| `@path/to/file` | Reference a file directly in your prompt |
| `/help` | List all commands |
| `/clear` | Wipe the conversation, start fresh (keeps CLAUDE.md context) |
| `/compact` | Summarize the conversation to free context space |
| `/model` | Switch models (e.g. Opus vs Sonnet vs Haiku) |
| `/usage` | Show plan limits & token usage (`/cost` is an alias) |

## CLI flags worth knowing

```bash
claude                        # interactive session
claude "fix the failing test" # start with an initial prompt
claude -p "explain db.py"     # print mode: answer and exit (great for scripts)
claude -c                     # continue most recent conversation
claude -r                     # resume: pick an older conversation
claude --model claude-sonnet-5   # pin a model
```

## Where things live

| Path | Purpose |
|---|---|
| `~/.claude/settings.json` | Your global settings (all projects) |
| `.claude/settings.json` | Project settings, committed to git — shared with your team |
| `.claude/settings.local.json` | Project settings, git-ignored — personal |
| `CLAUDE.md` | Project memory (chapter 4) |
| `.claude/skills/` | Custom slash commands / skills (chapter 5) |
| `.claude/agents/` | Custom subagents (chapter 5) |
| `~/.claude/projects/` | Conversation history per project |

## Next

→ [Chapter 2: Core Concepts](02-core-concepts.md) — understand *how* it works so you can predict what it will do.
