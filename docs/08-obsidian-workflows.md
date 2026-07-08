# 8. Obsidian Workflows — Your Vault as an Agent's Knowledge Base

An Obsidian vault is just a folder of markdown files. Claude Code's native tools — read, write, grep, glob — happen to be *perfect* for folders of markdown files. No plugins required, no sync services: run the agent inside the vault.

```bash
cd ~/ObsidianVault
claude
```

That's the whole setup. Everything below is technique.

## Give the vault a CLAUDE.md

Drop a `CLAUDE.md` in the vault root so every session knows your system:

```markdown
# My Vault

## Structure
- `Inbox/` — unprocessed notes, one idea per file
- `Projects/<name>/` — active work, each has an `_overview.md`
- `Areas/` — ongoing topics (health, finance, learning)
- `Archive/` — done; never edit, only move things here

## Conventions
- Filenames: `kebab-case.md`. Daily notes: `Daily/YYYY-MM-DD.md`.
- Every note gets YAML frontmatter: `tags`, `created`, `status`.
- Link generously with [[wikilinks]] — prefer linking over duplicating.
- Turkish or English content is fine; frontmatter always English.

## Rules
- Never delete a note; move to `Archive/` instead.
- Never rewrite my words in journal notes — only append.
```

The "never delete, never rewrite journals" rules matter: they encode the trust boundary between you and the agent, in a place the agent always sees.

## Recipe: the learning ritual

Studying a topic (a tool, a language, a cert)? Make the agent your note-taking scribe while you focus on the learning:

```
> we're doing lesson 4 of my nmap study. quiz me on lessons 1–3 first
> (read Learning/nmap/ for what we covered), then teach me OS detection.
> as we go, write clean notes to Learning/nmap/04-os-detection.md
> in the same style as the earlier lessons.
```

The agent reads its own previous notes, so **the course stays coherent across sessions** even though each session starts fresh. Your vault *is* the persistence layer.

## Recipe: inbox processing

```
> process Inbox/: for each note, add frontmatter (tags, created, status),
> fix the title, link related notes in Projects/ and Areas/,
> then move it to the right folder. show me the plan before moving anything.
```

Twenty minutes of filing becomes one review-and-approve.

## Recipe: project logs that write themselves

If you also *work* with Claude Code, end sessions with:

```
> summarize what we did and decided today into
> Projects/my-app/log/2026-07-08.md — decisions, open questions, next steps
```

Next session (in the code repo or the vault): *"read the latest log in the vault and pick up where we left off."* You've built continuity between coding sessions using notes as the bridge.

## Recipe: ask your own knowledge

Grep-powered recall across everything you've ever written:

```
> search the vault: what did I conclude about EV charging APIs last spring?
> find all notes that mention "OAuth" and contradict each other
> build a MOC (map of content) note for everything tagged #homelab
> what open questions appear in Projects/ that were never resolved?
```

This is where a five-year-old vault becomes an unfair advantage.

## Recipe: vault gardening

Periodic maintenance the agent is great at:

```
> find broken [[wikilinks]] and suggest fixes
> find near-duplicate notes and propose merges (don't merge yet)
> which notes have no incoming or outgoing links? suggest connections
> normalize frontmatter across Areas/ to match the schema in CLAUDE.md
```

Run in plan mode first; approve the batch.

## Working across code + vault at once

Two options:

1. **Run Claude in the parent folder** that contains both the repo and the vault (permission prompts will scope what it touches).
2. **Reference across roots:** start in the repo and say "also read `~/ObsidianVault/Projects/my-app/`" — you'll be asked to approve access outside the working directory once.

Pattern: specs and decisions live in the vault, code lives in the repo, and the agent reads both — "implement what we designed in the vault note" actually works.

## Safety rails for vault work

- The vault is personal and irreplaceable: **make it a git repo** (`git init`, commit daily — the agent can do this for you, and can also be *told* to commit before any bulk operation).
- Prefer plan mode for anything that moves/renames many files.
- A `PreToolUse` hook that blocks edits under `Journal/` enforces "append-only journals" mechanically ([chapter 6](06-hooks.md)).

## Next

→ [Chapter 9: Automation & Headless Mode](09-automation-and-headless.md) — Claude Code without a human at the keyboard.
