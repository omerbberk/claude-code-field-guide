# 2. Core Concepts — How It Actually Works

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

Understanding the machinery makes you dramatically better at using it. Claude Code is not magic; it's a well-designed loop.

## The agent loop

Every request goes through the same cycle:

```
your prompt
   ↓
Claude thinks → picks a tool → runs it → reads the result
   ↑___________________________________________|
   (repeats as many times as needed)
   ↓
final answer / finished work
```

When you ask "why is login broken?", Claude doesn't guess. It greps for `login`, reads the relevant files, maybe runs the test suite, reads the stack trace, forms a hypothesis, and verifies it. Each of those steps is a **tool call**.

## The built-in tools

| Tool | What it does |
|---|---|
| **Read** | Open a file (also images, PDFs, notebooks) |
| **Write / Edit** | Create files or make surgical string replacements |
| **Bash** | Run shell commands (tests, builds, git, anything) |
| **Grep / Glob** | Search file contents / find files by pattern |
| **WebSearch / WebFetch** | Search the web, read a page |
| **Task (subagents)** | Spawn a separate agent for a self-contained job |
| **TodoWrite** | Maintain a visible task checklist for multi-step work |

You don't call these yourself — Claude does. But knowing they exist changes how you prompt:

- ❌ "Here, let me paste the file for you…" → ✅ "look at `src/auth/session.ts`" (it will Read it)
- ❌ "I ran the tests, here's the output…" → ✅ "run the tests and fix the failures" (it will Bash them)

## The context window

Claude's working memory is the **context window** — everything it currently "sees": your conversation, the files it has read, tool outputs. It is large but finite, and quality degrades when it fills up with junk.

Practical implications:

1. **`/clear` between unrelated tasks.** Finished fixing the parser and now want to style a button? Clear. The parser conversation is dead weight.
2. **`/compact` mid-task** when a long session gets sluggish — it summarizes history and keeps going.
3. **Don't paste huge logs** — save them to a file and say "check `error.log`". Claude reads only the parts it needs.
4. **CLAUDE.md is loaded every session** — keep it dense and short (chapter 4).

## Models: pick your fighter

Claude Code lets you switch models with `/model`:

- **Opus-class** — deepest reasoning; hard architecture problems, gnarly debugging.
- **Sonnet-class** — the everyday workhorse; excellent at code, faster and cheaper.
- **Haiku-class** — fast and cheap; simple edits, scripted/headless jobs at scale.

A common pattern: plan in a stronger model, execute in a cheaper one. Or leave it on the default and forget about it — the default is chosen to be good.

## Extended thinking

For genuinely hard problems, ask Claude to think before acting — reasoning happens in a visible "thinking" phase:

```
> think hard about why this race condition only happens under load
```

Escalating phrases ("think", "think hard", "think harder", "ultrathink") allocate progressively more reasoning budget. Use it for design and debugging, not for routine edits — thinking costs time and tokens.

## Subagents

Claude can spawn **subagents** — separate Claude instances with their own clean context — for self-contained work:

- "Search the whole monorepo for places that construct SQL manually" → an *Explore* agent fans out and reports back only conclusions.
- A `code-reviewer` subagent that reviews diffs with its own instructions (see [`examples/agents/`](../examples/agents/)).

Why it matters: the subagent's noisy intermediate work (hundreds of file reads) never pollutes your main conversation. You get the summary, your context stays clean.

## Permissions & trust boundaries

Every tool call is checked against your permission settings before it runs:

- **Allow rules** — e.g. `Bash(npm test:*)` lets any `npm test …` run without asking.
- **Deny rules** — e.g. block reads of `.env` files entirely.
- **Ask** — the default for anything unmatched.

These live in `settings.json` files ([chapter 1](01-getting-started.md#where-things-live)) and are the foundation of safe automation. Details in [chapter 11](11-security-best-practices.md).

## The mental model that matters most

Treat Claude Code like a **very fast, very well-read new teammate**:

- It has read effectively all public code, but knows *nothing* about your project until it looks (or reads CLAUDE.md).
- It's excellent at executing a clear brief, and will make reasonable-but-maybe-wrong assumptions from a vague one.
- It verifies things when you ask it to ("run the tests", "check the types") — build that into your requests.
- Bad output is usually a prompting problem: missing context, missing constraints, or a task that should have been two tasks.

## Next

→ [Chapter 3: Everyday Workflows](03-everyday-workflows.md) — the recipes you'll use every single day.
