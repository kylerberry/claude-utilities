# Claude Utilities

Reusable agents, commands, and workflows for [Claude Code](https://claude.ai/claude-code).

## CRAFTS Workflow

This repo is built around **CRAFTS** — a structured development workflow for AI-assisted coding:

**Conceptualize > Render > Assess > Fix > Tighten > Sharpen**

See [CRAFT.md](CRAFT.md) for the full methodology, philosophy, and setup instructions.

## What's Included

```
.claude/
├── agents/
│   ├── planner.md      # Conceptualize phase — plans before code is written
│   └── reviewer.md     # Reference — Assess phase now uses /simplify skill
├── commands/
│   └── pr-fixup.md     # Post-push PR cleanup (conflicts, comments, CI)
├── skills/
│   └── security-scanning-security-hardening/
│       └── SKILL.md    # Tighten phase — multi-layer security hardening
```

### Agents

| Agent | CRAFTS Phase | Purpose |
|-------|-------------|---------|
| `planner` | **C**onceptualize | Reads specs and context, produces scope, test cases, implementation plan, and risks |

### Skills

| Skill | CRAFTS Phase | Purpose |
|-------|-------------|---------|
| `/simplify` | **A**ssess | Reviews changed code for reuse, quality, and efficiency |
| `security-scanning-security-hardening` | **T**ighten | Scans diff for security vulnerabilities and OWASP issues |

### Commands

| Command | Purpose |
|---------|---------|
| `/pr-fixup [pr]` | Checks a PR for merge conflicts, review comments, and CI failures — fixes them in order |

## Quick Start

Copy what you need into your project:

```bash
# Everything
cp -r .claude/ /path/to/your-project/.claude/

# Just the planner agent
cp -r .claude/agents/planner.md /path/to/your-project/.claude/agents/

# Just the pr-fixup command
mkdir -p /path/to/your-project/.claude/commands
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/

# Or symlink for auto-updates
ln -s /path/to/claude-utilities/.claude/agents/planner.md /path/to/your-project/.claude/agents/
ln -s /path/to/claude-utilities/.claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

Then add the CRAFTS workflow to your project's `CLAUDE.md` — see [CRAFT.md § Setting Up](CRAFT.md#setting-up-crafts-in-your-project) for the snippet.

## License

MIT
