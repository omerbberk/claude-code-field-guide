# 13. Token Efficiency — More Work per Token

Whether you're on a subscription (usage limits) or the API (per-token billing), the currency is the same: **tokens**. The good news: almost everything that saves tokens also makes the agent *smarter*, because the real enemy is context pollution, and pollution costs both money and quality.

## Where tokens actually go

Every turn, the model re-reads its entire context: system prompt, CLAUDE.md, MCP tool definitions, the whole conversation, every file it opened, every command output. Ranked by real-world impact:

1. **Conversation length** — a 3-hour session re-reads 3 hours of history every turn
2. **File reads** — especially whole huge files when 40 lines were needed
3. **Tool output** — test logs, build output, stack dumps
4. **Always-loaded overhead** — CLAUDE.md + every connected MCP server's tool definitions
5. **Thinking budget** — "ultrathink" on a rename you could have done with sed

## The big five habits

### 1. `/clear` aggressively

New task = new conversation. This is the highest-impact habit in this guide, and it's free. Carrying this morning's debugging session into this afternoon's CSS work means paying for the debugging session on *every single message* — while it actively distracts the model.

### 2. Scope what it reads

```
# expensive and vague:
> figure out how auth works

# cheap and precise:
> read src/auth/session.ts and middleware/verify.ts — how does token refresh work?
```

You usually know roughly where the answer lives. Saying so converts a 30-file exploration into a 2-file read. Same answer, ~10x fewer tokens.

### 3. Keep output out of the chat

- Big logs → to a file: "output is in `test.log`, check the failures" (Claude greps the relevant slice instead of holding 2,000 lines in context).
- Ask for quiet commands: `npm test 2>&1 | tail -30`, `--reporter=dot`, `git log --oneline`.
- In your own scripts: less stdout on success, detail only on failure — agents read what your tools print.

### 4. Delegate bulky reading to subagents

A research subagent reads 100 files in *its own* context and returns 20 lines of conclusions to yours ([chapter 12](12-agents-and-delegation.md)). You pay for the exploration **once**, instead of re-paying for it in every subsequent turn of your main session.

### 5. Match the model to the task

`/model` is a cost dial. Haiku-class for mechanical edits and bulk headless jobs (absurdly cheap), Sonnet-class for daily work, the big guns for genuinely hard problems. A common split: plan with a strong model, execute the plan with a cheaper one.

## Trim the fixed overhead

These load **every session**, forever, in every conversation:

- **CLAUDE.md** — keep it dense; link with `@docs/...` instead of inlining; prune dead rules quarterly ([chapter 4](04-context-claude-md-memory.md)).
- **MCP servers** — every connected server injects its tool definitions. Disconnect the ones you stopped using ([chapter 7](07-mcp-integrations.md)).

A bloated CLAUDE.md plus ten idle MCP servers can consume a meaningful slice of your context before you've typed a word.

## `/compact` vs `/clear`

- **`/clear`** — nuke it. Right choice between unrelated tasks. If needed, re-prime with one line: "we're mid-refactor of the billing module; tests in `billing.test.ts` are the spec."
- **`/compact`** — summarize and continue. Right choice mid-task when history is long but still relevant. You can steer it: `/compact keep the decisions about the API design`.
- **Auto-compact** triggers near the limit — fine as a seatbelt, but manual compaction at natural breakpoints (a milestone done, tests green) preserves better information.

Bonus: an *external memory file* beats both. Have the agent maintain `PROGRESS.md` — decisions made, current state, next steps — then `/clear` and reload just that file. Ten lines of curated state replaces thousands of tokens of raw history. (Vault users: this is the [project-log pattern](08-obsidian-workflows.md#recipe-project-logs-that-write-themselves).)

## Right-size the thinking

Extended thinking is a scalpel, not a default:

- Routine edit/rename/boilerplate → no thinking keywords at all
- Tricky bug, design decision → "think hard about…"
- Race conditions, subtle architecture trade-offs → the heavy budgets

Asking for maximum reasoning on trivial work is the token equivalent of hiring a consultant to rename a variable.

## Batch, don't drip

```
# 5 turns, 5x context re-reads:
> rename getUser to fetchUser        …then: > now in the tests
…

# 1 turn:
> rename getUser → fetchUser everywhere (src, tests, docs),
> update imports, run tests to confirm
```

Fewer, richer instructions — each turn you save is an entire context re-read you don't pay for. This also means: write the *definition of done* into the first message, so the agent doesn't burn turns asking what you meant.

## For API/headless users specifically

- **Prompt caching** makes repeated context (system prompt, CLAUDE.md) dramatically cheaper on consecutive calls within the cache window — rapid iteration is cheaper than sporadic pokes.
- `--max-turns` caps runaway loops; `--allowedTools` prevents wandering.
- Bulk jobs (triage, classification, lint-ish sweeps): Haiku + tight prompts + `--output-format json`.
- Check `/cost` per session; on the API, read the per-run cost in the JSON result metadata and graph it over time — regressions in your automation show up as cost spikes.

## The paradox worth internalizing

Token-efficient usage isn't ascetic usage. The goal is **spending tokens where they buy quality** — a thorough test run, a real code review, a well-briefed subagent — and not spending them on carrying dead conversation weight, unscoped exploration, and idle tool definitions. Lean context, generous verification. That combination is both the cheapest *and* the best-performing way to run Claude Code.

---

*End of the guide's core chapters. Drop-in configs live in [`examples/`](../examples/) — and if you've developed a token-saving trick not listed here, [PRs are welcome](../CONTRIBUTING.md).*
