# 6. Hooks — Deterministic Automation

CLAUDE.md says "please format code after editing". A hook **guarantees** it. Hooks are shell commands the harness itself runs at fixed points in the agent's lifecycle — the model can't forget them, skip them, or be talked out of them.

## Hook events

The most-used events (the full list is much longer — `SessionEnd`, `PreCompact`, `SubagentStop`, `PermissionRequest`, and more; see the [official hooks reference](https://code.claude.com/docs/en/hooks)):

| Event | Fires | Classic use |
|---|---|---|
| `PreToolUse` | before a tool call; **can block it** | guard protected files, lint the command |
| `PostToolUse` | after a tool call succeeds | auto-format edited files, run affected tests |
| `UserPromptSubmit` | when you submit a prompt | inject context, validate input |
| `Notification` | Claude needs your attention | desktop/phone notification |
| `Stop` | Claude finishes a turn | completion sound, trigger follow-up checks |
| `SessionStart` | session begins | load env info, warm caches |

## Anatomy

Hooks live in `settings.json` (user, project, or local — see [chapter 1](01-getting-started.md#where-things-live)). Each hook has a **matcher** (which tool) and a **command**:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | { read f; case \"$f\" in *.ts|*.tsx) npx prettier --write \"$f\" ;; esac; }"
          }
        ]
      }
    ]
  }
}
```

That's the classic **format-on-edit** hook: every time Claude edits a TypeScript file, Prettier runs on it. No instruction needed, no way to forget.

The hook receives JSON on stdin describing the event (tool name, inputs, etc.) — hence the `jq`.

## Blocking with PreToolUse

A `PreToolUse` hook that exits with code 2 **blocks the action**, and its stderr is shown to Claude as feedback:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | grep -qE '(^|/)(\\.env|secrets/|prod\\.config)' && { echo 'BLOCKED: protected file' >&2; exit 2; } || exit 0"
          }
        ]
      }
    ]
  }
}
```

Now *nothing* — no prompt, no confusion, no bug — lets the agent touch `.env` or `secrets/`. This is defense in depth on top of permission rules.

## More recipes

**Log every command Claude runs** (audit trail):

```json
{
  "matcher": "Bash",
  "hooks": [{
    "type": "command",
    "command": "jq -r '\"[\" + (now|todate) + \"] \" + .tool_input.command' >> ~/.claude/bash-audit.log"
  }]
}
```

**Desktop notification when Claude needs you** (macOS):

```json
{
  "matcher": "",
  "hooks": [{
    "type": "command",
    "command": "osascript -e 'display notification \"Claude needs input\" with title \"Claude Code\"'"
  }]
}
```

(put it under `"Notification"`) — start a long task, go make tea, get pinged when it's your turn.

**Run related tests after edits:** a `PostToolUse` script that maps `src/foo.ts` → `src/foo.test.ts` and runs it, feeding failures straight back into the loop.

Copy-paste-ready versions of all of these live in [`examples/hooks/`](../examples/hooks/).

## Configuring without hand-editing JSON

Run `/hooks` for an interactive editor, or just ask:

```
> set up a hook that runs prettier on any file you edit
```

(Claude Code is good at configuring itself. Review what it writes, though — hooks run with *your* shell privileges.)

## Hooks vs CLAUDE.md — the decision rule

> If "usually" is good enough → CLAUDE.md.
> If it must happen **every time, provably** → hook.

Formatting, audit logs, protected paths, notifications: hooks. Style preferences, conventions, context: CLAUDE.md.

## A word of caution

Hooks execute arbitrary shell commands automatically, with your permissions. Treat third-party hook snippets like any code you'd run: read them first. Keep hooks fast (they run inline) and quiet on success.

## Next

→ [Chapter 7: MCP & Integrations](07-mcp-integrations.md) — plug the agent into GitHub, browsers, databases, and the rest of your world.
