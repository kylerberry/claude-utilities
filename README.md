# Claude Utilities

Reusable agents, commands, and workflows for [Claude Code](https://claude.ai/claude-code).

## CRAFT Workflow

This repo is built around **CRAFT** — a structured development workflow for AI-assisted coding:

**Conceptualize > Render > Assess > Fix > Transform**

See [CRAFT.md](CRAFT.md) for the full methodology, philosophy, and setup instructions.

## What's Included

```
.claude/
├── agents/
│   ├── planner.md      # Conceptualize phase — plans before code is written
│   └── reviewer.md     # Assess phase — reviews code before commit
├── commands/
│   └── pr-fixup.md     # Post-push PR cleanup (conflicts, comments, CI)
```

### Agents

| Agent | CRAFT Phase | Purpose |
|-------|-------------|---------|
| `planner` | **C**onceptualize | Reads specs and context, produces scope, test cases, implementation plan, and risks |
| `reviewer` | **A**ssess | Reviews uncommitted changes for spec compliance, architecture violations, test quality |

### Commands

| Command | Purpose |
|---------|---------|
| `/pr-fixup [pr]` | Checks a PR for merge conflicts, review comments, and CI failures — fixes them in order |

## Quick Start

Copy what you need into your project:

```bash
# Everything
cp -r .claude/ /path/to/your-project/.claude/

# Just the agents
cp -r .claude/agents/ /path/to/your-project/.claude/agents/

# Just the pr-fixup command
mkdir -p /path/to/your-project/.claude/commands
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/

# Or symlink for auto-updates
ln -s /path/to/claude-utilities/.claude/agents/planner.md /path/to/your-project/.claude/agents/
ln -s /path/to/claude-utilities/.claude/agents/reviewer.md /path/to/your-project/.claude/agents/
ln -s /path/to/claude-utilities/.claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

Then add the CRAFT workflow to your project's `CLAUDE.md` — see [CRAFT.md § Setting Up](CRAFT.md#setting-up-craft-in-your-project) for the snippet.

## License

MIT
