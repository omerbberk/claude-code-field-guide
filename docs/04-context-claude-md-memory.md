# 4. CLAUDE.md & Memory — Teach It Once

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

If you find yourself telling Claude the same thing twice, that thing belongs in a file. This chapter is the highest ROI in the whole guide.

## What is CLAUDE.md?

A markdown file that Claude Code automatically loads at the start of **every** session in that project. It's your project's standing orders: conventions, commands, warnings, context.

Generate a starting point:

```
> /init
```

…then edit it by hand. It's just markdown; own it like code (commit it, review changes to it).

## The hierarchy

Claude merges CLAUDE.md files from several levels, most specific last:

| File | Scope | Typical content |
|---|---|---|
| `~/.claude/CLAUDE.md` | **You**, all projects | personal style, preferred tools, language |
| `CLAUDE.md` (repo root) | everyone on the project | build commands, architecture, conventions |
| `CLAUDE.local.md` / local memory | you, this project | your sandbox URLs, local quirks (git-ignored) |
| `subdir/CLAUDE.md` | that subtree | module-specific rules, loaded when working there |

Monorepo tip: a small `CLAUDE.md` per package beats one giant root file.

## CLAUDE.md vs `.claude/rules/*.md` vs auto-memory

CLAUDE.md isn't the only place Claude Code stores what it knows about your project. Three mechanisms now overlap, and picking the right one keeps context lean instead of bloated with everything, always, everywhere.

| | CLAUDE.md | `.claude/rules/*.md` | Auto-memory |
|---|---|---|---|
| **Written by** | You, deliberately | You, deliberately | Claude, automatically during sessions |
| **Loaded** | Always, every session | Always, or scoped by path/glob if the rule declares one | When Claude judges it relevant |
| **Best for** | Small, load-bearing core: commands, architecture, hard constraints | Larger rule sets split by area (`frontend.md`, `db-migrations.md`) so unrelated work doesn't pay their context cost | Things learned mid-session you didn't write down — a debugging insight, a preference you stated once |
| **Review** | Commit, review diffs in PR | Commit, review diffs in PR | Inspect and prune with `/memory` |

Rough rule of thumb: if it's a permanent, non-negotiable fact about the project, it belongs in **CLAUDE.md**. If it's a real rule set but only relevant to one part of the codebase, split it into **`.claude/rules/`** so it's not dead weight everywhere else. If it's something Claude picked up on its own that you haven't consciously curated yet, it's sitting in **auto-memory** — run `/memory` periodically to see what's accumulated there and promote anything durable into CLAUDE.md or a rules file, the same way you'd `#`-save a correction (see below).

None of this replaces the `#` shortcut or the hierarchy above — auto-memory and `.claude/rules/` are additional storage tiers, not a different system. The mental model stays the same: the more permanent and universal a fact is, the higher up it should live.

## What good CLAUDE.md content looks like

Dense, factual, imperative. It's loaded every session, so every line costs context — make each one earn its place.

```markdown
# MyApp

## Commands
- Build: `npm run build`   Test: `npm test`   One test: `npm test -- -t "name"`
- NEVER run `npm run deploy` — CI owns deployment.

## Architecture
- Monorepo: `apps/web` (Next.js), `apps/api` (Fastify), `packages/shared`.
- All DB access goes through `packages/db` repositories. No raw SQL in apps.

## Conventions
- TypeScript strict; no `any` without an inline justification comment.
- Errors: throw `AppError` subclasses, never plain strings.
- Tests colocated: `foo.ts` → `foo.test.ts`.

## Gotchas
- `apps/api` needs Node 20; the web app breaks on Node 22.
- The staging DB is shared — never run destructive migrations against it.
```

Anti-patterns:

- ❌ Prose essays about the project's history
- ❌ Things Claude can trivially discover itself (file listings, obvious structure)
- ❌ Stale commands nobody updated (wrong docs are worse than no docs)

## The `#` shortcut

Mid-session, prefix a message with `#` to save a memory without breaking flow:

```
# always use pnpm in this repo, never npm
```

Claude asks which memory file to store it in. This is how CLAUDE.md grows organically — capture rules *the moment* you notice yourself correcting the agent.

## The feedback loop that makes agents feel "trained"

1. Claude does something you don't like (uses `npm` instead of `pnpm`).
2. You correct it in chat — it's fixed *for this session*.
3. **You `#` it into memory — it's fixed *forever*.**

Skip step 3 and you'll be correcting the same thing next week. Do it consistently and after a month the agent feels eerily tuned to your team.

## Importing other files

CLAUDE.md supports `@path` imports, so you can point at docs that already exist instead of duplicating them:

```markdown
See @docs/architecture.md for the service topology.
API conventions: @docs/api-style.md
```

Claude pulls those in when relevant. Keep the always-loaded core tiny; link the rest.

## Personal vs team memory

- **Team facts** (build commands, conventions) → repo `CLAUDE.md`, committed.
- **Personal preferences** ("answer briefly", "I prefer functional style") → `~/.claude/CLAUDE.md`.
- **Personal project-specific** (your local ports, your test account) → local memory, git-ignored — and still no real secrets; see [chapter 11](11-security-best-practices.md).

## Maintain it like code

- Review `CLAUDE.md` diffs in PRs — a bad instruction poisons every future session.
- Prune quarterly. If a rule never fires, delete it.
- When Claude repeatedly gets something wrong *despite* a rule, the rule is probably ambiguous — rewrite it with an example.

## Next

→ [Chapter 5: Slash Commands & Skills](05-slash-commands-and-skills.md) — package whole workflows into one command.
