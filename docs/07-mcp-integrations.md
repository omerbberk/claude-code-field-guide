# 7. MCP & Integrations — What Should I Connect?

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/how-it-works/issues) — re-verifying a chapter is a great first PR.

Out of the box, Claude Code can touch anything reachable from your shell. **MCP (Model Context Protocol)** extends that: servers that give the agent new tools — query a database, drive a browser, read your issue tracker.

The question everyone asks: *"which ones should I actually connect?"* Answer below.

## How to add a server

```bash
# stdio server (runs locally as a subprocess)
claude mcp add --transport stdio playwright -- npx @playwright/mcp@latest

# HTTP/SSE server (remote, often OAuth)
claude mcp add --transport http github https://api.githubcopilot.com/mcp/

# list / inspect / remove
claude mcp list
claude mcp get github
claude mcp remove github
```

Scopes: `--scope local` (you, this project — default), `--scope project` (committed to `.mcp.json`, shared with the team), `--scope user` (you, everywhere).

Inside a session, `/mcp` shows connection status and handles OAuth logins.

## The honest starter list

Connect these first — highest value, lowest fuss:

| Server | Gives Claude | Why it earns a slot |
|---|---|---|
| **GitHub** | issues, PRs, CI status, reviews | "look at issue #42 and fix it", "why is CI red?" — though the `gh` CLI alone covers 80% of this without MCP |
| **Playwright / Puppeteer** | a real browser | *the* frontend unlock: Claude opens your app, clicks around, screenshots, verifies its own UI changes |
| **Context7 / docs servers** | up-to-date library docs | kills "the API changed since training" errors |
| **Your database** (Postgres/SQLite MCP) | schema + queries | "why is this query slow?", "what's actually in this table?" — use a **read-only** connection |
| **Sentry / error tracking** | production errors | "top crash this week → root cause → fix PR" in one conversation |

Solid second tier, if they're part of your life:

- **Slack** — post summaries, read a channel for context
- **Linear / Jira / Notion** — tickets and specs in, status updates out
- **Figma** — read designs while building UI
- **Filesystem** (scoped) — grant access to a folder *outside* the repo, e.g. your notes vault ([chapter 8](08-obsidian-workflows.md))

## The rule: don't hoard servers

Every connected server injects its tool descriptions into context and widens your attack surface. A setup with 15 MCP servers is slower, dumber, and less safe than one with 4 you use daily.

> Connect when you feel the pain, disconnect what you stopped using. `claude mcp list` is your junk drawer — clean it.

## Design your own integration surface

Anything with a CLI doesn't need MCP at all. Claude Code already speaks:

- `gh` (GitHub), `aws`, `gcloud`, `az`, `kubectl`, `docker`, `terraform`
- `curl` against any API (put the base URL + auth pattern in CLAUDE.md, keep the token in an env var)
- your own scripts — document them in CLAUDE.md and they become tools

A line like this in CLAUDE.md is a poor man's MCP server, and often all you need:

```markdown
## Internal API
- Staging API: `curl -H "Authorization: Bearer $STAGING_TOKEN" https://staging.example.com/api/...`
- Never call the production API directly.
```

## Verification loops: the real reason to integrate

The pattern behind every great integration: **give the agent a way to check its own work.**

- Frontend change → Playwright opens the page and screenshots it
- Backend change → run the test suite, hit the endpoint with curl
- DB migration → query the schema afterwards
- Bug fix from Sentry → confirm the stack trace path is dead

An agent with a feedback loop converges on correct. An agent without one converges on *plausible*. Choose integrations that close loops, not ones that look cool.

## Security notes (short version — full version in chapter 11)

- Treat MCP servers as **code you're executing**. Prefer official/first-party servers; pin versions.
- Data returned by a server (web pages, issue comments) can contain **prompt injection**. Claude Code has mitigations, but don't point powerful write-tools at untrusted content with permissions wide open.
- Database access: read-only credentials, non-production replicas.
- OAuth tokens are stored by Claude Code; revoke server access you no longer use.

## Next

→ [Chapter 8: Obsidian Workflows](08-obsidian-workflows.md) — the same agent, pointed at your knowledge instead of your code.
