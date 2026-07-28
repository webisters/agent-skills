# Webisters Agent Skills

AI agent skills and rules for working inside [Webisters PHP framework](https://webisters.com) projects. Drop these files into your project and your AI agent (Claude Code, Cursor, Codex) will understand the framework's conventions, service container, routing, and commands out of the box.

## What's included

```
CLAUDE.md                          # Generic project rules (Claude Code, any agent)
AGENTS.md                          # OpenAI Codex / compatible agents
.claude/agents/
  webisters-new-project.md         # Scaffold new projects with webisters new-*
  webisters-routing.md             # Define and inspect routes
  webisters-database.md            # Configure and query the database service
  webisters-make.md                # Generate controllers, models, views
  webisters-testing.md             # Write and run PHPUnit tests
  webisters-config-env.md          # Config files and .env.php
.cursor/rules/
  webisters.mdc                    # Cursor IDE rules (always-on)
```

## Installation

### Option A - Copy files into your project

```bash
composer require webisters/agent-skills
```

Then copy the files you need from `vendor/webisters/agent-skills/` into your project root.

### Option B - Clone and copy manually

```bash
git clone https://github.com/webisters/agent-skills
```

Copy `CLAUDE.md`, `AGENTS.md`, `.claude/`, and/or `.cursor/` into your Webisters project root.

### Which files to copy

| Agent / Tool | Files to copy |
|---|---|
| Claude Code CLI or IDE extension | `CLAUDE.md` and/or `.claude/agents/*.md` |
| Cursor | `.cursor/rules/webisters.mdc` |
| OpenAI Codex | `AGENTS.md` |
| Any other agent | `CLAUDE.md` (generic, works everywhere) |

## Requirements

- A Webisters project created with `webisters new-app` (or `new-api`, `new-one`, `new-site`)
- PHP >= 8.2

## License

MIT
