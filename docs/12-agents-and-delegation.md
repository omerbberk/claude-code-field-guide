# 12. Agents & Delegation — Working Like a Tech Lead

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

The mental shift that unlocks serious throughput: stop thinking "I have an AI assistant" and start thinking "I lead a small team of agents." This chapter is about who the team members are and which tasks to hand to whom.

## The cast

| Agent | Context | Best at |
|---|---|---|
| **Main session** | your full conversation | the task you're actively steering |
| **Explore subagent** | fresh, read-only tools | fan-out search & codebase survey; returns conclusions only |
| **Plan subagent** | fresh | designing implementation strategies for big changes |
| **Custom subagents** (`.claude/agents/`) | fresh + your instructions | recurring roles: reviewer, tester, doc-writer, security auditor |
| **Parallel sessions** (worktrees) | fully independent | second workstream — a real second pair of hands |
| **Headless runs** (`claude -p`) | one-shot | scheduled/scripted jobs, CI ([chapter 9](09-automation-and-headless.md)) |

## Why delegate at all? Context, again.

A subagent runs in its **own context window** and reports back only its conclusion. That has two superpowers:

1. **Your main context stays clean.** A repo-wide survey might read 200 files — delegated, that's 200 reads you never carry around for the rest of your session.
2. **Specialists get specialist instructions.** A reviewer agent told to be ruthless *stays* ruthless, uncontaminated by three hours of implementation chat where it "knows what you meant".

Cost note: a subagent spends its own tokens, so don't delegate trivia. Delegate when the work is *bulky* (lots of reading) or needs *fresh eyes* — that's exactly when it also saves tokens overall ([chapter 13](13-token-efficiency.md)).

## Which tasks go to which agent

**Delegate to Explore/research agents:**
```
> use an agent to find every place we construct SQL strings manually
> have an agent survey how the 8 services do config loading — table, not code dumps
```
Broad questions with narrow answers. Perfect delegation material.

**Delegate to a reviewer (fresh eyes are the point):**
```
> have the code-reviewer agent go over my current diff before I open the PR
```
The implementing session is biased toward its own code. A fresh context isn't.

**Keep in the main session:**
- The feature you're actively building (you're steering turn by turn)
- Anything needing your taste/decisions mid-flight
- Quick, single-file work — delegation overhead beats the gain

**Send to a parallel session (worktree):**
- A genuinely independent second task (feature B while main does feature A)
- Long-running migrations/upgrades you check on occasionally

**Send to headless/CI:**
- Anything you do on a schedule or per-PR — nightly digests, review passes, dependency triage

## Defining a custom role

`.claude/agents/security-auditor.md`:

```markdown
---
name: security-auditor
description: Audits code for security issues. Use for pre-release checks
  or when touching auth, crypto, or user input handling.
tools: Read, Grep, Glob
---

You are a security auditor. Review the code you're pointed at for:
injection (SQL/command/path), authn/authz gaps, secrets in code,
unsafe deserialization, missing input validation at trust boundaries.

Report findings as: severity / file:line / issue / concrete fix.
No findings? Say so plainly. You do not edit files.
```

Details that matter:

- **`description` drives auto-delegation** — Claude reads it when deciding whether to hand a task to this agent. Write it like a "when to use me" note.
- **`tools` is a security boundary**: read-only tools = an auditor that *cannot* "helpfully fix" things.
- Keep each role narrow. Five sharp specialists beat one "do-everything-well" prompt.

Manage roles with `/agents`. A practical starter team: `code-reviewer`, `test-writer`, `security-auditor`, plus the built-in Explore/Plan.

## Patterns from the field

**Fan-out research → synthesize.** For "how do our services handle retries?", multiple research agents can sweep different areas in parallel and the main session merges their reports. Minutes instead of an afternoon.

**Writer / critic loop.** Main session implements → reviewer agent critiques → main session addresses findings. Two or three rounds approximates a real PR review cycle *before* the PR exists.

**Pipeline stages as roles.** Spec (Plan) → implement (main) → test (test-writer) → audit (security-auditor) → document (doc-writer). Each stage starts with clean context and a stage-shaped brief; quality is visibly higher than one long mega-session.

**The babysitter.** A headless cron job that checks CI every morning and *files issues* (never pushes fixes) for anything red. Agents that report generously but mutate conservatively are the ones you'll still trust in six months.

## Delegation hygiene

1. **Brief like a manager, not a mind-reader**: subagents don't see your conversation. Include the goal, constraints, scope, and expected output format in the delegation.
2. **Demand a deliverable**: "return a table of file / issue / suggested fix" — not "look into it".
3. **Trust but verify**: findings from a research agent are leads; spot-check the important ones.
4. **Don't over-orchestrate.** If a task fits comfortably in your main session, delegating it is slower and costs more. Delegation is for bulk, parallelism, fresh eyes, and repetition — not for its own sake.

## Next

→ [Chapter 13: Token Efficiency](13-token-efficiency.md) — do all of the above while spending less.
