# Translations 🌍

The guide's source of truth is the English version in [`docs/`](../docs/). Translations live here, one folder per language (ISO 639-1 code):

```
translations/
  tr/    # Türkçe — started
  es/    # Español — wanted
  pt/    # Português — wanted
  zh/    # 中文 — wanted
  ...    # your language here
```

## How to contribute a translation

1. **Start with the README** — `translations/<lang>/README.md` is the highest-value single file and a perfectly sized first PR.
2. **Then chapters, one per PR** — mirror the filenames in `docs/` (e.g. `translations/tr/01-getting-started.md`).
3. **Keep technical terms in English** where practitioners do: don't translate command names, flags, file paths, or terms like *hook*, *prompt*, *context window* if your community uses the English word.
4. **Keep code blocks and prompts as-is** — translate the surrounding prose, not the commands.
5. Add a line at the top: `> Translation of [docs/XX-name.md](../../docs/XX-name.md) — English version is the source of truth.`

Partial translations are fine and expected — a language folder with three chapters helps three chapters' worth of readers. If a chapter changed upstream since it was translated, updating the translation is a great PR too.

## Maintainers per language

Want to be the go-to reviewer for your language? Open an issue and say so — we'll list you here.

| Language | Status | Maintainer |
|---|---|---|
| Türkçe (tr) | README done, chapters welcome | — |
