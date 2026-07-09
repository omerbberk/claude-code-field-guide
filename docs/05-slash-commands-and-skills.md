# 5. Slash Commands & Skills

Slash commands are how you trigger packaged behavior. Some are built in; the interesting part is writing your own.

## Useful built-in commands

| Command | What it does |
|---|---|
| `/init` | Generate CLAUDE.md for the project |
| `/clear` | Reset the conversation |
| `/compact` | Summarize history to free context |
| `/model` | Switch model |
| `/permissions` | View/edit allow & deny rules |
| `/mcp` | Manage MCP server connections |
| `/agents` | Manage subagents |
| `/hooks` | Configure hooks interactively |
| `/config` | Settings (theme, notifications, …) |
| `/review` | Quick read-only review of a GitHub PR |
| `/code-review` | Deeper diff review (supports `--fix` to apply findings) |
| `/rewind` | Roll conversation *and code* back to a checkpoint |
| `/context` | Visualize what's filling your context window |
| `/usage` | Plan limits & token/cost usage (`/cost` is an alias) |
| `/plugin` | Manage plugins (bundles of skills/agents/hooks) |
| `/doctor` | Diagnose installation issues |
| `/vim` | Vim keybindings, if that's your thing |

## Custom skills: package a workflow

A **skill** is a folder with a `SKILL.md` file: instructions + optional metadata that Claude loads when you invoke it (or when it detects the skill is relevant). Project skills live in `.claude/skills/`, personal ones in `~/.claude/skills/`. (Older-style custom commands in `.claude/commands/*.md` still work — commands were merged into skills.)

```
.claude/skills/
  changelog/
    SKILL.md
```

```markdown
---
name: changelog
description: Update CHANGELOG.md from commits since the last release tag
---

# Changelog updater

1. Find the latest git tag. If none, use the first commit.
2. Read commits since then (`git log <tag>..HEAD --oneline`).
3. Group into: Added / Changed / Fixed / Removed.
4. Ignore merge commits, version bumps, and formatting-only commits.
5. Write the section at the top of CHANGELOG.md under an Unreleased header.
6. Show me the section before saving.
```

Now `/changelog` runs that whole procedure. Working example in [`examples/skills/changelog/`](../examples/skills/changelog/).

### Arguments

Skills receive arguments — `$ARGUMENTS` (everything) or `$1`, `$2` (positional):

```markdown
---
name: fix-issue
description: Fix a GitHub issue by number
---
Fetch GitHub issue #$1 with `gh issue view $1`, understand it,
locate the relevant code, implement a fix, and open a PR referencing the issue.
```

Usage: `/fix-issue 42`.

### What makes a good skill

- **A procedure you repeat** — release prep, dependency audit, writing a new API endpoint "our way".
- **A checklist people forget** — pre-commit review, security pass, i18n check.
- **A house style** — "write a commit message like *we* write commit messages".

Rule of thumb: the third time you type the same multi-step instruction, turn it into a skill.

## Subagents: specialists with their own context

A **subagent** is a named agent definition — its own system prompt, its own tool access, its own clean context window. Defined in `.claude/agents/*.md` (project) or `~/.claude/agents/` (personal), managed via `/agents`.

```markdown
---
name: code-reviewer
description: Reviews diffs for correctness, security, and maintainability.
tools: Read, Grep, Glob, Bash
---

You are a strict senior reviewer. Review the current git diff.
Priorities: correctness > security > performance > style.
For each finding: file:line, severity, a concrete fix.
You do not edit files — report only.
```

Uses:

- **Delegation:** "have the code-reviewer agent look at my diff" — the review happens in a separate context and only findings come back.
- **Restricted power:** give a subagent read-only tools so it *can't* modify anything.
- **Parallelism:** several subagents can research different corners of a repo simultaneously.

Full example in [`examples/agents/`](../examples/agents/).

## Skills vs subagents vs CLAUDE.md — which one?

| You want to… | Use |
|---|---|
| State a permanent fact/rule ("we use pnpm") | **CLAUDE.md** |
| Trigger a repeatable procedure on demand | **Skill** |
| Run a role with different instructions/permissions in a clean context | **Subagent** |
| React automatically to events, deterministically | **Hook** ([chapter 6](06-hooks.md)) |

## Next

→ [Chapter 6: Hooks](06-hooks.md) — when "please always do X" needs to become "X literally always happens".
