# Hook recipes

Copy the JSON fragments below into the `"hooks"` section of your `settings.json`
(user: `~/.claude/settings.json`, project: `.claude/settings.json`).
Background in [chapter 6 of the guide](../../docs/06-hooks.md).

All hooks receive event JSON on stdin — that's what the `jq` calls parse.

## 1. Auto-format every file Claude edits (PostToolUse)

```json
{
  "matcher": "Edit|Write",
  "hooks": [{
    "type": "command",
    "command": "jq -r '.tool_input.file_path' | { read f; case \"$f\" in *.ts|*.tsx|*.js|*.jsx) npx prettier --write \"$f\" >/dev/null 2>&1 ;; *.py) black -q \"$f\" 2>/dev/null ;; *.go) gofmt -w \"$f\" ;; esac; }"
  }]
}
```

## 2. Block edits to protected files (PreToolUse — exit 2 blocks)

```json
{
  "matcher": "Edit|Write",
  "hooks": [{
    "type": "command",
    "command": "jq -r '.tool_input.file_path' | grep -qE '(^|/)(\\.env|secrets/|prod\\.config)' && { echo 'BLOCKED: protected file' >&2; exit 2; } || exit 0"
  }]
}
```

## 3. Audit log of every shell command (PostToolUse)

```json
{
  "matcher": "Bash",
  "hooks": [{
    "type": "command",
    "command": "jq -r '\"[\" + (now|todate) + \"] \" + .tool_input.command' >> ~/.claude/bash-audit.log"
  }]
}
```

## 4. Desktop notification when Claude needs input (Notification, macOS)

```json
{
  "matcher": "",
  "hooks": [{
    "type": "command",
    "command": "osascript -e 'display notification \"Claude needs your input\" with title \"Claude Code\"'"
  }]
}
```

Linux equivalent: replace the command with `notify-send "Claude Code" "Claude needs your input"`.

## 5. Sound when a long task finishes (Stop, macOS)

```json
{
  "matcher": "",
  "hooks": [{
    "type": "command",
    "command": "afplay /System/Library/Sounds/Glass.aiff"
  }]
}
```

## Tips

- Keep hooks **fast** — they run inline with the agent loop.
- Keep hooks **quiet on success** — noisy output pollutes context ([chapter 13](../../docs/13-token-efficiency.md)).
- Test a hook by asking Claude to do the thing it guards and watching it fire.
- `/hooks` inside a session gives you an interactive editor.
