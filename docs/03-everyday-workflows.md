# 3. Everyday Workflows

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

Real recipes, in roughly the order you'll need them. Each shows the *shape* of a good prompt — adapt freely.

## Understand an unfamiliar codebase

```
> give me a tour of this codebase: entry points, main modules, how data flows
> where is payment processing implemented? walk me through a purchase end to end
> what would break if I upgraded Express to v5?
```

Claude explores with grep/read instead of guessing. For very large repos, ask for a map first, then zoom: *"now focus on the billing module"*.

## Fix a bug

The highest-leverage habit: **give it the evidence, ask for the loop**.

```
> the app crashes when a user with no profile picture opens settings.
> here's the stack trace in crash.log. find the root cause, fix it,
> and add a regression test that fails without the fix.
```

Notice the structure: symptom → evidence → definition of done. "Fix it and prove it's fixed" beats "fix it" every time.

## TDD with an agent (surprisingly great)

```
> write failing tests for a rate limiter: 10 req/min per user, sliding window,
> 429 with Retry-After header. don't implement anything yet.

# review the tests, then:

> now implement until all tests pass. don't modify the tests.
```

Because the agent can *run* the tests, it iterates until green — and "don't modify the tests" keeps it honest.

## Refactor safely

```
# Step 1 — plan mode (Shift+Tab): no files touched
> I want to extract the email-sending logic into its own service with an
> interface, so we can swap providers. plan the refactor.

# Step 2 — review the plan, adjust, then approve execution

# Step 3 — verify
> run the full test suite and the type checker
```

For sweeping mechanical changes ("rename this concept everywhere"), ask for a dry run first: *"list every file you'd change and how, before changing anything."*

## Git, commits, and PRs

Claude Code is genuinely good at git:

```
> commit this — write a proper message based on what actually changed
> create a branch, split my uncommitted changes into two logical commits
> what changed between v2.1 and v2.2 that could affect the API?
> open a PR: summarize the changes, note the risky parts, add a test plan
```

It reads `git diff`/`git log` itself and writes messages describing the *why*, not just the *what*. (It uses the `gh` CLI for GitHub operations — install and auth once, benefit forever.)

## Code review

```
> review this diff like a strict senior engineer: correctness first,
> then edge cases, then style. tell me what you'd block the PR over.
> /review 142        # review GitHub PR #142
```

Reviewing your *own* work with a fresh session (`/clear` first!) catches things the "author" session is blind to.

## Multi-step tasks: let it keep a checklist

For anything with 3+ steps, Claude maintains a visible todo list — you'll see items get checked off as it works. You can steer mid-flight:

```
> skip the docs update for now, prioritize getting CI green
```

And `Esc` at any moment pauses it so you can redirect. Interrupting is not rude; it's the workflow.

## Run things in parallel

Two independent tasks? Open two terminals, two `claude` sessions, same repo (or use git worktrees to avoid stepping on each other):

```bash
git worktree add ../myapp-featureB
# terminal 1: claude in the main checkout, working on feature A
# terminal 2: claude in ../myapp-featureB, working on feature B
```

This is the single biggest throughput unlock for heavy users.

## Everyday non-code chores

Claude Code is a general terminal agent — not only for source code:

```
> convert all PNGs in ./assets to webp and update references
> this CSV has inconsistent date formats — normalize to ISO 8601
> summarize what changed in package-lock.json and whether anything is risky
> draft release notes from the commits since the last tag
```

## Prompting patterns that consistently work

| Pattern | Example |
|---|---|
| **Definition of done** | "…and it's done when `npm test` and `npm run lint` both pass" |
| **Constraints up front** | "don't add new dependencies", "keep the public API unchanged" |
| **Point at examples** | "follow the pattern used in `UserService.ts`" |
| **Scope fences** | "only touch files under `src/billing/`" |
| **Ask for options first** | "give me 2–3 approaches with trade-offs before implementing" |

## Next

→ [Chapter 4: CLAUDE.md & Memory](04-context-claude-md-memory.md) — stop repeating yourself; teach it once.
