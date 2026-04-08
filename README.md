# Claude Utilities

Reusable commands, skills, and workflows for [Claude Code](https://claude.ai/claude-code).

## CRAFTS Workflow

This repo is built around **CRAFTS** — a structured development workflow for AI-assisted coding:

**Conceptualize > Render > Assess > Fix > Tighten > Sharpen**

See [CRAFTS.md](CRAFTS.md) for the full methodology, philosophy, and setup instructions.

## What's Included

```
.claude/
├── skills/
│   └── security-scanning-security-hardening/
│       └── SKILL.md    # Tighten phase — multi-layer security hardening
├── commands/
│   └── pr-fixup.md     # Post-push PR cleanup (conflicts, comments, CI)
```

### Built-in Claude Code Tools Used

CRAFTS leans on Claude Code's built-in capabilities rather than custom agents:

| Tool | CRAFTS Phase | Purpose |
|------|-------------|---------|
| `/plan` | **C**onceptualize | Built-in planning agent — scope, test cases, implementation plan, risks |
| `/simplify` | **A**ssess | Built-in skill — reviews code for quality, reuse, and efficiency |

### Bundled Skills

| Skill | CRAFTS Phase | Purpose |
|-------|-------------|---------|
| `security-scanning-security-hardening` | **T**ighten | Multi-layer security hardening across app, infra, and compliance |

### Commands

| Command | Purpose |
|---------|---------|
| `/pr-fixup [pr]` | Checks a PR for merge conflicts, review comments, and CI failures — fixes them in order |

## Quick Start

Copy what you need into your project:

```bash
# The security hardening skill
cp -r .claude/skills/ /path/to/your-project/.claude/skills/

# The pr-fixup command
mkdir -p /path/to/your-project/.claude/commands
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/

# Or symlink for auto-updates
ln -s /path/to/claude-utilities/.claude/skills/ /path/to/your-project/.claude/skills
ln -s /path/to/claude-utilities/.claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

Then add the CRAFTS workflow to your project's `CLAUDE.md` — see [CRAFTS.md § Setting Up](CRAFTS.md#setting-up-crafts-in-your-project) for the snippet.

## License

MIT
