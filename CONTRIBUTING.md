# Contributing

This guide gets better with real-world experience — especially **yours**. You don't need to be an expert to contribute; "here's a workflow that saved me an hour" is exactly the content this repo exists for.

## Easiest ways to contribute

1. **Share a recipe.** A prompt pattern, a hook, a skill, an MCP setup, an Obsidian workflow — anything you actually use. Add it to the relevant chapter or to `examples/`.
2. **Fix something wrong or stale.** Claude Code evolves fast; if a command or behavior described here has changed, a small correcting PR is gold.
3. **Improve clarity.** If a section confused you, it confuses others. Rewrites welcome.
4. **Translate nothing, simplify instead.** The repo stays English-only for now; PRs that simplify language help non-native readers most.
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
git clone https://github.com/omerbberk/how-it-works.git
cd how-it-works
# edit, then open a PR against main
```

If you use Claude Code itself to write your contribution, mention it in the PR — dogfooding stories are part of the fun.

Thanks! 🙌
