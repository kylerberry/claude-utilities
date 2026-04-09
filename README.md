# Claude Utilities

A methodology and toolkit for building software with AI coding agents — from raw idea to shipped product.

Two frameworks, used in sequence:

1. **ORACLE** — structured kickoff process. Takes a raw idea through adversarial refinement, spec creation, and project scaffolding before any code is written.
2. **CRAFTS** — execution workflow. A disciplined phase-by-phase loop for writing, reviewing, and hardening code.

---

## ORACLE

**Originate > Red-Team > Amend > Constitution > Layout > Execute**

Most AI-assisted projects fail not because of bad code, but because of bad thinking upstream. ORACLE is the process that fixes this — it forces the hard questions before a line of code exists.

| Phase | What happens |
|-------|-------------|
| **O**riginate | Riff the raw idea with LLM A into a v1 proposal |
| **R**ed-Team | Switch to LLM B — adversarial review of the proposal |
| **A**mend | Return to LLM A — reconcile, fill gaps, harden. Repeat R→A until it holds |
| **C**onstitution | Turn the proposal into a thorough product spec — the law of the project |
| **L**ayout | Map every skill and domain the codebase will need |
| **E**xecute | Init CLAUDE.md, install skills, generate a phased task list, begin CRAFTS |

The multi-LLM red-teaming loop (R→A) is the core insight: a different model with no stake in the idea will surface assumptions your original agent rationalized away.

**→ [ORACLE.md](ORACLE.md)** — prompt template, ready to paste into a new conversation.

---

## CRAFTS

**Conceptualize > Render > Assess > Fix > Tighten > Sharpen**

CRAFTS is the loop you run on every task. It prevents the most common AI coding failure: generating plausible-looking code that doesn't hold up under review, security scrutiny, or real use.

| Phase | What happens |
|-------|-------------|
| **C**onceptualize | `/plan` — scope, test cases, implementation plan, risks. Human reviews before proceeding. |
| **R**ender | TDD strictly. Red → Green → Refactor. No implementation before a failing test. |
| **A**ssess | `/simplify` — fresh-context review of the diff for quality, reuse, efficiency |
| **F**ix | Address blocking issues from Assess |
| **T**ighten | `security-scanning-security-hardening` — scan the diff, fix all findings |
| **S**harpen | Commit, push, update domain CLAUDE.md with lessons learned |

Each phase uses Claude Code's built-in tools rather than custom agents — `/plan` for Conceptualize, `/simplify` for Assess. The security hardening skill is the only external dependency.

**→ [CRAFTS.md](CRAFTS.md)** — CLAUDE.md drop-in, ready to copy into your project.

---

## What's Included

```
.claude/
├── skills/
│   └── security-scanning-security-hardening/   # Tighten phase
├── commands/
│   └── pr-fixup.md                             # Post-push PR cleanup
```

### `/pr-fixup` — PR Cleanup Command

A Claude Code slash command that unblocks a PR in one shot. Run `/pr-fixup [pr-number]` (or just `/pr-fixup` on the current branch) and it will:

1. **Resolve merge conflicts** — merges the base branch, reads both sides, preserves intent of both changes
2. **Address review comments** — fixes valid feedback, drafts replies for anything it disagrees with, never silently ignores
3. **Fix CI failures** — diagnoses lint, type check, test, and build failures and fixes the root cause (not just the symptom)
4. **Output a summary** — tells you exactly what was fixed, what's still failing, and whether the PR is ready to merge

Pairs naturally with the end of a CRAFTS Sharpen phase.

### Copy into your project

```bash
# Security hardening skill (required for Tighten phase)
cp -r .claude/skills/ /path/to/your-project/.claude/skills/

# PR cleanup command (optional but recommended)
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

### Add CRAFTS to your CLAUDE.md

See [CRAFTS.md](CRAFTS.md) for the drop-in block.

### Start a new project with ORACLE

See [ORACLE.md](ORACLE.md) for the prompt template.

---

## Philosophy

**Planning beats replanning.** Wrong assumptions caught before code exists cost minutes. Caught after implementation they cost hours, sometimes days. Both ORACLE and CRAFTS front-load the thinking.

**Different models catch different things.** ORACLE's red-team phase deliberately switches LLMs because a fresh model with no context has no incentive to rationalize your weak assumptions. It will find gaps your original model talked itself out of.

**Self-review is impossible.** The builder can't see their own blind spots. CRAFTS uses `/simplify` — a fresh-context review pass — specifically because the agent that wrote the code is the worst reviewer of it.

**Security is a gate, not an afterthought.** The Tighten phase runs before every commit. Vulnerabilities found pre-commit cost minutes. Found post-deploy they cost far more.

**Knowledge compounds.** The Sharpen phase updates domain CLAUDE.md files with lessons learned after every task. Week 1 those files are stubs. Week 8 they're handbooks that make every subsequent task faster and less error-prone.

---

## License

MIT
