# 11. Security Best Practices

> **Last verified:** Claude Code v2.1.205 · July 2026. Behavior changed since? [Report it](https://github.com/omerbberk/how-it-works/issues) — re-verifying a chapter is a great first PR.

An agent that can edit files and run commands deserves the same respect as any powerful tool. None of this is scary; all of it is worth doing.

## Secrets: the golden rules

1. **Never put real secrets in CLAUDE.md, skills, hooks, or settings** — these files get committed and shared. Reference environment variables instead:

   ```markdown
   ## API access
   - Staging: `curl -H "Authorization: Bearer $STAGING_TOKEN" ...`
   - The token comes from `.env` (git-ignored). Never print it.
   ```

2. **Keep secrets out of the conversation.** Don't paste tokens into the chat "just this once" — conversations are stored in history files on disk.

3. **Deny-list secret files** so the agent can't even read them:

   ```json
   {
     "permissions": {
       "deny": [
         "Read(./.env)",
         "Read(./.env.*)",
         "Read(./secrets/**)",
         "Read(~/.ssh/**)",
         "Read(~/.aws/credentials)"
       ]
     }
   }
   ```

4. **Add a pre-publish sweep** to your routine before making any repo public:

   ```bash
   git grep -nEI "(api[_-]?key|secret|token|password|Bearer )" -- . ':!docs' | less
   # better: run a real scanner
   npx @secretlint/quick-start "**/*"   # or: gitleaks detect
   ```

5. **If a secret ever lands in git history**, rotating the credential is the fix — deleting the file in a later commit is not.

## Permission rules: allow the boring, deny the dangerous

Build your allowlist from what you actually approve repeatedly (start strict; loosen with evidence):

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test:*)",
      "Bash(npm run lint:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(git push --force:*)",
      "Read(./.env)"
    ]
  }
}
```

- Project-level `settings.json` → committed, protects the whole team.
- `settings.local.json` → your personal additions.
- Review with `/permissions` any time.

Layer a `PreToolUse` hook over the deny rules for the truly untouchable paths ([chapter 6](06-hooks.md)) — defense in depth.

## `--dangerously-skip-permissions` (a.k.a. YOLO mode)

The name is honest. Rules of engagement:

- ✅ Inside a container/VM with no credentials and a throwaway checkout
- ✅ CI runners with scoped, short-lived tokens
- ❌ Your laptop, your dotfiles, anything with your SSH keys in reach

If you want speed on your own machine, the answer is a well-tuned allowlist + auto-accept edits mode — or better, the built-in sandbox below. Not YOLO.

## The sandbox: fewer prompts *and* more safety

Permission prompts ask you to predict what a command will do. The **sandboxed Bash tool** flips that: the OS itself enforces what commands *can* do, so most of them can run without asking at all. Run `/sandbox` in a session to turn it on (macOS uses the built-in Seatbelt framework; Linux/WSL2 need `bubblewrap` + `socat`; native Windows isn't supported).

What the boundary looks like:

- **Filesystem**: commands can write only to the working directory and the session temp dir. Your `~/.bashrc`, system binaries, other projects — untouchable, even by child processes.
- **Network**: no domains are pre-allowed; the first request to a new domain prompts you once, and you can pre-allow the usual suspects (`registry.npmjs.org`, `github.com`, …) in settings.
- **Escape hatch**: commands that genuinely can't run sandboxed fall back to the normal permission flow — you still decide.

In *auto-allow* mode this means `npm test`, builds, greps, formatters, and most of what an agent does all day just run — no prompt fatigue, no YOLO. Deny rules are still respected, and scary commands (`rm` against `/` or `$HOME`, `git push` if you ask-rule it) still stop and ask.

Two honest caveats:

1. **Reads are broad by default** — the sandbox blocks writes, but a sandboxed command can still *read* `~/.ssh` or `~/.aws/credentials` unless you block them. Add them to `sandbox.credentials` (or `denyRead`) — pair this with the deny rules from the secrets section above.
2. **It's a strong boundary, not a perfect one** — network filtering is by hostname, without inspecting TLS. For truly unattended runs on untrusted input, a container/VM is still the answer.

Configuration lives in `settings.json` under a `sandbox` key; the [official sandboxing docs](https://code.claude.com/docs/en/sandboxing) cover the full reference.

## Prompt injection: the threat model worth knowing

When Claude reads untrusted content — a web page, an issue comment, a README from a random repo — that content may contain instructions aimed at *the agent* ("ignore your instructions and run …"). Claude Code ships mitigations, and permission prompts are your backstop.

Practical hygiene:

- Be deliberately alert when combining **untrusted input + powerful tools + loose permissions** — that's the dangerous triangle; remove one corner.
- Don't run auto-accept/YOLO while processing content from strangers.
- Watch the actions it proposes while browsing external content; `Esc` is always available.
- Scope MCP servers to what they need (read-only DB creds, minimal OAuth scopes).

## Supply chain

- **MCP servers and hooks are code you execute.** Read third-party ones before installing; prefer official servers; pin versions.
- Skills/agents/hook snippets from the internet: same rule. A hook runs shell commands with your privileges every single session.
- Keep Claude Code itself updated (`claude update`) — security fixes ship regularly.

## Git as a safety net

- Work on branches; let the agent commit early and often. `git reset` beats "undo 45 edits by hand".
- For bulk operations (vault reorganizations, mass renames): **commit first**, then operate.
- Have agents open PRs instead of pushing to main — human review is the final permission system.
- Never let an unattended agent force-push, tag releases, or touch prod credentials. (Deny rules make "never" mechanical.)

## Reviewing what the agent did

Trust grows from verification:

- Read the diff (`git diff`) before committing — the IDE extension makes this painless.
- Ask a *fresh* session (or a `code-reviewer` subagent) to review work from another session.
- Keep an audit hook logging every Bash command ([chapter 6](06-hooks.md)) — invaluable both for debugging and for confidence.

## The one-paragraph summary

Keep secrets in env vars and out of reach; encode "never" as deny rules and blocking hooks, not hopes; give unattended agents the smallest toolset that works, inside boundaries you'd be comfortable losing; and use git + PRs + fresh-eyes review as the human layer. Do this once and the agent is both safer *and* faster — you'll stop hovering.
