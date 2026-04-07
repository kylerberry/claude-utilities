# Claude Utilities

Reusable agents, skills, commands, and templates for [Claude Code](https://claude.ai/claude-code).

## Structure

```
.claude/
├── agents/       # Sub-agents (planner, reviewer, etc.)
├── commands/     # Slash commands (/pr-fixup, etc.)
└── skills/       # Skills with rich context
```

## Commands

### `/pr-fixup [pr-number-or-url]`

Checks a PR for merge conflicts, review comments, and CI failures — then fixes them. Detects the current branch's PR if no argument is provided.

**What it does:**
1. Resolves merge conflicts against the base branch
2. Reads and addresses review comments (fixes valid ones, explains disagreements)
3. Pulls CI check results and fixes any failures

## Usage

Copy the files you want into your project's `.claude/` directory, or symlink them:

```bash
# Copy a command
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/

# Or symlink
ln -s /path/to/claude-utilities/.claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

## License

MIT
