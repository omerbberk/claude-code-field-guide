# 14. Troubleshooting & FAQ

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/claude-code-field-guide/issues) — re-verifying a chapter is a great first PR.

Real failures, grouped by area, in **symptom → cause → fix** form. If you hit something not listed here, [open an issue](https://github.com/omerbberk/claude-code-field-guide/issues) — this chapter is meant to grow.

## Install & auth

| Symptom | Cause | Fix |
|---|---|---|
| `claude: command not found` after install | Install script put the binary somewhere not on `PATH` (common on fresh shells/WSL) | Reopen the terminal, or manually add the reported install dir to `PATH`; `claude doctor` also flags this |
| Stuck on the login screen / browser doesn't open | Headless environment (SSH session, container) with no browser to redirect to | Use `claude setup-token` for a manual, browser-free login flow |
| "Invalid API key" but the key looks right | Key was exported in one shell but Claude Code is reading a different env, or a stale key is cached in `settings.json` | `echo $ANTHROPIC_API_KEY` in the *same* terminal you're launching from; check `~/.claude/settings.json` for an overriding key |
| Works in terminal, fails in CI | CI runner has no persisted login state — subscription auth doesn't transfer | Use an API key (not subscription login) for headless/CI; see [chapter 9](09-automation-and-headless.md) |
| `claude doctor` reports version mismatch after update | Old binary cached alongside the new one (multiple install methods layered — brew + curl script, etc.) | Pick one install method, uninstall via the other, reinstall clean |

## Permissions

| Symptom | Cause | Fix |
|---|---|---|
| Every single edit prompts for approval, even repeat actions | No allow rules configured — default is ask-every-time | Add durable allow rules with `/permissions` instead of clicking "Yes" in each session |
| Claude tries a command and immediately gets denied with no prompt | The command matches a deny rule (often inherited from an org/team `settings.json`) | Check `/permissions` for deny entries; org-level policy may need to change, not your local config |
| Permission prompt appears but the "Yes" you clicked didn't stick next session | You approved for "this session only," not "always" | Choose the "always allow" option in the prompt, or add the rule directly via `/permissions` |
| Plan mode won't let Claude touch files at all | Working as intended — plan mode (Shift+Tab) is read-only by design | Exit plan mode once you approve the plan; it's a review gate, not a bug |

## Context & memory

| Symptom | Cause | Fix |
|---|---|---|
| Claude "forgot" a rule you told it | It was said in chat, not saved to memory — chat history isn't durable across sessions | `#` it into CLAUDE.md the moment you notice yourself repeating it (see [chapter 4](04-context-claude-md-memory.md)) |
| Responses get slow and vague after hours in one session | Context window is bloated with accumulated back-and-forth | `/compact` to summarize and continue, or `/clear` + a one-line recap of where things stand |
| Claude is confidently wrong about your stack/architecture | CLAUDE.md is missing, stale, or was never generated | Run `/init` if you haven't, or audit the existing file against reality — wrong docs are worse than no docs |
| A rule in `.claude/rules/` doesn't seem to apply | Rule is path-scoped and you're working outside its glob | Check the rule's declared scope; broaden it or move the rule to CLAUDE.md if it should always apply |
| Auto-memory captured something wrong or outdated | Claude wrote an inference to memory that turned out to be incorrect or context-specific | Run `/memory`, find the entry, delete or correct it directly |

## MCP & integrations

| Symptom | Cause | Fix |
|---|---|---|
| MCP server shows connected but tools never get called | Server is registered but its tools aren't relevant to the phrasing of your request | Reference the integration explicitly ("use the Linear MCP to…") until Claude's tool selection catches on from context |
| MCP server fails to connect on startup | Auth token expired, or the server process needs credentials not present in this shell | Re-run the server's auth flow; for local servers, confirm required env vars are set in the same shell Claude Code launches from |
| Every session got noticeably slower after adding MCP servers | Each connected server's tool definitions load into context on every session start | Prune servers you're not actively using; see [chapter 10](10-tips-tricks-cost.md#cost-control-mostly-relevant-for-api-billing) |
| A remote MCP server works from the CLI but not inside a headless script | Headless invocations may need the server URL/auth passed explicitly rather than relying on interactive session config | Pass MCP config explicitly via the flags documented for your automation entrypoint; see [chapter 9](09-automation-and-headless.md) |

## IDE (VS Code / JetBrains)

| Symptom | Cause | Fix |
|---|---|---|
| Inline diffs stopped appearing in the editor | IDE extension disconnected from the CLI session (common after an editor restart or extension update) | Reload the IDE window; reconnect from the extension's status indicator |
| Extension installed but nothing happens when Claude runs | Claude Code CLI and the IDE extension are running against different workspace roots | Make sure the terminal you launch `claude` from is opened at the same root the IDE has open |
| Keybindings conflict with existing IDE shortcuts | Default Claude Code bindings overlap with editor-native ones | Remap via `~/.claude/keybindings.json` (see [chapter 10](10-tips-tricks-cost.md#quality-of-life-settings)) |
| Diffs show but accepting/rejecting them does nothing | Extension version mismatch with the installed CLI version | Update both to matching versions; `claude doctor` flags CLI-side mismatches |

## FAQ

**Is my code sent anywhere I don't control?**
See [chapter 11](11-security-best-practices.md) for what's local vs transmitted, and how to scope what Claude can access.

**Why does the same prompt behave differently across sessions?**
Different accumulated context (CLAUDE.md changes, memory entries, conversation history) — Claude Code isn't stateless run-to-run the way a raw API call is. `/context` shows what's currently loaded.

**Subscription or API — which should I use?**
Subscription (Pro/Max) for regular interactive use; API for headless/CI/bulk automation where you need per-token cost control. See [chapter 9](09-automation-and-headless.md) and [chapter 10](10-tips-tricks-cost.md).

**Something here didn't fix it — now what?**
Run `claude doctor` first; it catches most install/version/config issues automatically. If it's genuinely new, [open an issue](https://github.com/omerbberk/claude-code-field-guide/issues) with your symptom — that's exactly how this chapter grows.

## Next

→ You've reached the end of the core chapters. Drop-in configs live in [`examples/`](../examples/), and community workflows live in [`recipes/`](../recipes/).