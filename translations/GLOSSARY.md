# Glossary

Short definitions for terms this guide uses often. Each links to the chapter that covers it in depth.

**Agent loop** — The read → act → observe cycle Claude Code runs on: it reads relevant files or output, decides on a tool call (edit a file, run a command), observes the result, and repeats until the task is done or it needs your input. See [Core Concepts](docs/02-core-concepts.md).

**CLAUDE.md** — A markdown file you keep in your project root that Claude reads automatically at the start of a session. It holds persistent context: conventions, architecture notes, commands to run, things to avoid. See [CLAUDE.md & Memory](docs/04-context-claude-md-memory.md).

**Checkpoint** — A saved snapshot of the conversation and file state that lets you roll back to an earlier point if an edit goes wrong, without losing the whole session. See [Getting Started](docs/01-getting-started.md).

**Context window** — The finite amount of text (measured in tokens) Claude can hold in memory at once for a given session — your prompt, file contents, tool outputs, and conversation history all count against it. See [Core Concepts](docs/02-core-concepts.md).

**Headless mode** — Running Claude Code non-interactively, typically from a script or CI pipeline, with no terminal UI to click through — just an input and a captured output. See [Automation & Headless](docs/09-automation-and-headless.md).

**Hook** — A deterministic script that runs automatically at a defined point in Claude's workflow (before a tool call, after a file edit, etc.), used to enforce rules Claude might otherwise forget — like auto-formatting or blocking edits to protected files. See [Hooks](docs/06-hooks.md).

**MCP (Model Context Protocol)** — An open protocol for connecting Claude Code to external tools and data sources (databases, issue trackers, APIs) through a standard server interface, instead of one-off custom integrations. See [MCP & Integrations](docs/07-mcp-integrations.md).

**Sandbox** — An isolated execution environment Claude Code can run commands in without touching your real filesystem or network, useful for testing risky operations safely. See [Security Best Practices](docs/11-security-best-practices.md).

**Skill** — A custom, reusable capability you define for Claude — often a slash command or a documented procedure with a name and instructions — so it doesn't have to be re-explained in every session. See [Slash Commands & Skills](docs/05-slash-commands-and-skills.md).

**Subagent** — A separate Claude instance spawned to work on a scoped subtask (like a code reviewer or a test writer) with its own context and instructions, coordinated by the main session. See [Agents & Delegation](docs/12-agents-and-delegation.md).

**Worktree** — A Git feature that lets you check out multiple branches of the same repo into separate directories simultaneously, useful for running Claude on parallel tasks without branch-switching collisions. See [Everyday Workflows](docs/03-everyday-workflows.md).