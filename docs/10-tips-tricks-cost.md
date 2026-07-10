# 10. Tips, Tricks & Cost Control

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

The unglamorous habits that separate "I tried it" from "it changed how I work."

## Prompting tips that actually move the needle

1. **State the definition of done.** "…done when tests pass and `tsc` is clean" turns a hope into a loop the agent can close.
2. **Give scope fences.** "Only touch `src/billing/`" prevents helpful-but-unwanted refactors elsewhere.
3. **Course-correct early.** `Esc` the moment you see it going sideways. Ten seconds of interruption saves ten minutes of undoing.
4. **Ask for the plan when stakes are high.** Plan mode (Shift+Tab) for anything architectural.
5. **One task, one conversation.** Mixing tasks muddies context; `/clear` is free.
6. **Point at examples, not adjectives.** "Like `UserService.ts`" beats "clean and idiomatic".
7. **Let it verify.** Any request that ends with "…and prove it works" produces better code.

## Session hygiene

- `/clear` between unrelated tasks — the single most impactful habit.
- `/compact` when a long session starts feeling dull or slow; the model summarizes and continues.
- Rewind with double-`Esc` (or `/rewind`) when a thread went wrong — checkpoints can restore the *code* too, so it beats arguing with a bad approach.
- Long-running work? Kick it off, and let a `Notification` hook ping you ([chapter 6](06-hooks.md)) instead of watching it scroll.

## Speed & throughput

- **Parallel sessions** via git worktrees ([chapter 3](03-everyday-workflows.md#run-things-in-parallel)) — two or three agents on independent tasks is a normal way to work.
- **Subagents for research** — "use an agent to survey how errors are handled across the repo" keeps the noisy exploration out of your main context.
- **Queue follow-ups**: you can keep typing while Claude works; messages queue and it handles them in order.
- **Tab-complete file paths**, `@file` references, and `!command` for quick shell checks without leaving the session.

## Cost control (mostly relevant for API billing)

Subscription users (Pro/Max) mostly need usage awareness — limits reset every few hours. API users pay per token; here's where tokens go:

| Cost driver | Mitigation |
|---|---|
| Bloated context (huge sessions) | `/clear` often, `/compact` long tasks |
| Rereading giant files | point to specific files/dirs instead of "look everywhere" |
| Strong model on trivial work | `/model` down to Sonnet/Haiku for routine edits & bulk jobs |
| Extended thinking everywhere | reserve "think hard" for genuinely hard problems |
| Fat CLAUDE.md + 15 MCP servers | prune both — they're loaded every session |

Check `/usage` for plan limits and per-session cost (`/cost` is an alias), and `/context` to see *what* is eating your context window. For headless bulk work, Haiku-class models are absurdly cost-effective.

## Quality-of-life settings

- `/config` — theme, notification preferences, spinner, verbosity.
- `/vim` — modal editing in the input box.
- **Custom status line** — show model/branch/cost in your prompt (`/statusline` sets it up).
- **Keybindings** — remappable via `~/.claude/keybindings.json`.
- **IDE extension** — inline diffs in VS Code/JetBrains while the agent works; great for reviewing edits as they happen.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Claude "forgot" a rule you told it | It's in chat history, not memory — `#` it into CLAUDE.md |
| Slow/weird after hours of work | `/compact`, or `/clear` + a one-line recap |
| Edits keep hitting permission prompts | add allow rules via `/permissions` instead of clicking Yes forever |
| It changes things you didn't ask for | add scope fences; put "don't refactor unrelated code" in CLAUDE.md |
| Install/update problems | `claude doctor` |
| It's confidently wrong about your stack | your CLAUDE.md is missing (or wrong) — fix the source of truth |

## The meta-tip

When something annoys you twice, don't adapt — **encode the fix**: a CLAUDE.md line, a hook, a skill, a permission rule. Claude Code is less a tool you use and more a system you *grow*. People who invest an hour in configuration get it back weekly.

## Next

→ [Chapter 11: Security Best Practices](11-security-best-practices.md) — the chapter you shouldn't skip.
