# 9. Automation & Headless Mode

Everything so far assumed you at a keyboard. This chapter removes you from the loop: scripts, CI, scheduled jobs, and bots built on Claude Code.

## Print mode: `claude -p`

The foundation of all automation. One prompt in, result out, exit:

```bash
claude -p "summarize the changes in the last 5 commits"

# structured output for scripts
claude -p "list all TODO comments with file:line as JSON" --output-format json

# pipe things in
cat error.log | claude -p "what's the root cause? one paragraph"

# stream progress for long jobs
claude -p "run the test suite and fix simple failures" --output-format stream-json
```

Useful flags for headless runs:

| Flag | Purpose |
|---|---|
| `--output-format json` | machine-readable result (includes cost/duration metadata) |
| `--allowedTools "Read,Grep,Bash(git log:*)"` | explicit tool whitelist for this run |
| `--max-turns 10` | hard cap on agent loop iterations |
| `--model claude-haiku-4-5` | cheap model for bulk jobs |
| `--append-system-prompt "..."` | extra standing instructions |

**Permissions are the crux:** headless runs can't ask you anything. Either whitelist exactly what's needed (`--allowedTools`), or — for fully autonomous runs — use a sandboxed environment. Never `--dangerously-skip-permissions` on a machine you care about ([chapter 11](11-security-best-practices.md)).

## Unix-style composition

Claude Code behaves like a good citizen in pipelines:

```bash
# triage in bulk
for f in logs/*.log; do
  claude -p "is there anything alarming here? YES/NO + one line" < "$f"
done

# gate a commit on an AI check (exit code reflects success)
git diff --cached | claude -p "any obvious bugs or leaked secrets in this diff? \
  if yes, list them and exit nonzero" || exit 1
```

## GitHub Actions

The official action (`anthropics/claude-code-action`) turns `@claude` mentions into working PRs:

```yaml
# .github/workflows/claude.yml
name: Claude
on:
  issue_comment:
    types: [created]
jobs:
  claude:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: read
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

Then, on any issue: *"@claude fix this and open a PR"*. The API key lives in **GitHub Secrets** — never in the workflow file.

Other CI patterns that pull their weight:

- **AI review pass** on every PR (comment findings, never auto-merge)
- **Nightly dependency triage**: "check for security advisories against our lockfile, open an issue if any are critical"
- **Docs drift check**: "does README still match the CLI flags in src/cli.ts?"

## Scheduled jobs (cron)

Anything `claude -p` can do, cron can do on a schedule:

```bash
# crontab -e — weekday morning repo digest at 08:00
0 8 * * 1-5 cd ~/work/myapp && claude -p "summarize yesterday's commits and \
  any new/updated issues; write it to ~/reports/$(date +\%F).md" \
  --allowedTools "Read,Grep,Bash(git log:*),Bash(gh issue list:*),Write"
```

Ideas that work well on a timer:

- Morning digest of commits/issues/CI status → a markdown report or Slack message
- Vault gardening ([chapter 8](08-obsidian-workflows.md)) once a week, in a git-committed vault
- "Check that the backup job actually produced a file last night; alert me if not"

Start with **report-only** jobs (write a file, send a message). Graduate to jobs that *change* things only after weeks of trustworthy reports — and even then, have them open PRs rather than push.

## The Agent SDK

When a shell script isn't enough, the same engine is available as a library — the **Claude Agent SDK** (TypeScript & Python):

```ts
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "audit this repo's error handling and write findings to AUDIT.md",
  options: { allowedTools: ["Read", "Grep", "Glob", "Write"] },
})) {
  if (message.type === "result") console.log(message.result);
}
```

You get the full agent loop — tools, permissions, hooks, MCP, subagents — programmable: custom tools, permission callbacks, session control. This is the path from "I use an agent" to "I ship products built on agents."

## Design rules for unattended agents

1. **Smallest possible toolset.** A digest job needs `Read`/`Grep`/`git log` — not `Write`, not network.
2. **Cap the blast radius**: `--max-turns`, timeouts, sandbox/container, throwaway credentials.
3. **Make output verifiable**: reports, diffs, PRs — artifacts a human reviews. Not silent mutations.
4. **Log everything** (a `PostToolUse` audit hook — [chapter 6](06-hooks.md)).
5. **Idempotence**: running the job twice should be harmless.

## Next

→ [Chapter 10: Tips, Tricks & Cost Control](10-tips-tricks-cost.md) — the accumulated small stuff that compounds.
