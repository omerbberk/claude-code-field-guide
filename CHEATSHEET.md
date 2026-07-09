# Claude Code Cheatsheet

> **Last verified:** Claude Code v2.1.205 · July 2026. One page; details in the [chapters](README.md#table-of-contents).

## Install & start

```bash
curl -fsSL https://claude.ai/install.sh | bash   # macOS/Linux/WSL (Windows PS: irm https://claude.ai/install.ps1 | iex)
cd your-project && claude                        # start a session
claude update                                    # update by hand (native installs auto-update)
claude doctor                                    # diagnose problems
```

## Keys

| Key | Action |
|---|---|
| `Esc` | Interrupt mid-action — safe, it stops and waits |
| `Esc Esc` | Rewind conversation/code to a checkpoint |
| `Shift+Tab` | Cycle permission modes (normal → auto-accept → plan) |
| `Ctrl+C` | Cancel input / exit |
| `Tab` | Complete file paths |

## Prompt prefixes

| Prefix | Effect |
|---|---|
| `!command` | Run a shell command yourself; output lands in the conversation |
| `#note` | Save a memory to CLAUDE.md without breaking flow |
| `@path/to/file` | Reference a file directly |
| `think hard about …` | Allocate extended reasoning (escalates: think → think hard → think harder → ultrathink) |

## Slash commands

| Command | Does |
|---|---|
| `/init` | Generate CLAUDE.md for the project |
| `/clear` | New conversation (highest-impact habit) |
| `/compact` | Summarize history, keep going (steerable: `/compact keep the API decisions`) |
| `/rewind` | Checkpoints: roll back conversation and/or code |
| `/model` | Switch model |
| `/permissions` | Allow/deny rules |
| `/context` | What's filling the context window |
| `/usage` | Plan limits & cost (`/cost` alias) |
| `/mcp` | MCP servers & OAuth |
| `/agents` | Subagents |
| `/hooks` | Hook config |
| `/plugin` | Plugins |
| `/review` | Quick PR review · `/code-review` deeper (supports `--fix`) |
| `/sandbox` | Sandboxed Bash: OS-enforced boundaries, fewer prompts |
| `/statusline` `/config` `/vim` | QoL: status line, settings, vim mode |

## CLI flags

```bash
claude "fix the failing test"     # start with a prompt
claude -p "explain db.py"         # print mode: answer & exit (scripts)
claude -c                         # continue latest conversation
claude -r                         # resume: pick one
claude --model claude-sonnet-5    # pin a model
claude -p "…" --output-format json --max-turns 10 \
  --allowedTools "Read,Grep,Bash(git log:*)"      # headless, fenced
```

## Where things live

| Path | What |
|---|---|
| `CLAUDE.md` | Project memory, loaded every session |
| `~/.claude/CLAUDE.md` | Your personal memory, all projects |
| `.claude/settings.json` | Project settings (committed) |
| `.claude/settings.local.json` | Personal project settings (git-ignored) |
| `~/.claude/settings.json` | Global settings |
| `.claude/skills/` · `.claude/agents/` | Custom skills · subagents |
| `.mcp.json` | Shared MCP servers |

## Prompting patterns that work

- **Definition of done**: "…done when `npm test` and `tsc` pass"
- **Scope fence**: "only touch `src/billing/`"
- **Point at examples**: "follow the pattern in `UserService.ts`"
- **Evidence + loop** (bugs): symptom → stack trace file → "fix it and add a regression test"
- **Dry run** (bulk edits): "list every file you'd change, change nothing yet"
- **Batch, don't drip**: one rich instruction beats five follow-ups

## Token hygiene (30 seconds)

`/clear` between tasks · scope file reads · big logs to a file, not the chat · delegate bulky research to subagents · `/model` down for routine work · check `/context`.

## Safety minimums

Secrets in env vars, never in CLAUDE.md/chat · deny-rules for `.env`, `~/.ssh` · `--dangerously-skip-permissions` only in containers — on your own machine use allowlists + `/sandbox` · agents open PRs, humans merge.
