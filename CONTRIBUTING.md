# Contributing

This guide gets better with real-world experience — especially **yours**. You don't need to be an expert to contribute; "here's a workflow that saved me an hour" is exactly the content this repo exists for.

## Easiest ways to contribute

1. **Share a recipe.** A prompt pattern, a hook, a skill, an MCP setup, an Obsidian workflow — anything you actually use. Drop a single file into [`recipes/`](recipes/) (copy [`_template.md`](recipes/_template.md)), or add it to the relevant chapter or `examples/`.
2. **Fix something wrong or stale.** Claude Code evolves fast; if a command or behavior described here has changed, a small correcting PR is gold. Each chapter carries a **"Last verified: vX.Y.Z"** banner — re-checking a chapter against the current release and bumping that banner (fixing whatever drifted) is a complete, valued contribution on its own.
3. **Improve clarity.** If a section confused you, it confuses others. Rewrites welcome.
4. **Translate.** Translations live in [`translations/<lang>/`](translations/) — the README of your language is a perfect first PR, chapters go one per PR. (Simplifying the English also helps non-native readers and is equally welcome.)
5. **Open an issue** if you don't have time for a PR — "this didn't work for me on Windows" is a valuable contribution too.

## Recipe format

When adding a workflow/recipe, follow the house pattern:

```markdown
## Recipe: <what it achieves>

<one or two sentences: the situation and why this approach wins>

    > the actual prompt(s), as a quoted block

<optional: caveats, variations>
```

Keep prompts real — the exact shape you'd actually type, with placeholder names (`your-project`, `YOUR_API_KEY`).

## Ground rules

- **No secrets, ever.** No real API keys, tokens, e-mails, internal hostnames — placeholders only. PRs containing anything that looks like a credential will be closed.
- **Tested, not theorized.** Only submit workflows/configs you've actually run.
- **English**, friendly, concrete. Short sentences beat clever ones.
- One topic per PR — small PRs merge fast.

## Setup

There's nothing to build — it's all markdown.

```bash
git clone https://github.com/omerbberk/claude-code-field-guide.git
cd claude-code-field-guide
# edit, then open a PR against main
```

If you use Claude Code itself to write your contribution, mention it in the PR — dogfooding stories are part of the fun.

Thanks! 🙌
