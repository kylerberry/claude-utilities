# Claude Utilities

A skill-based toolkit for building software with AI coding agents — installable slash commands and primary agents that enforce disciplined, repeatable workflows.

## Skills

```
skills/
├── craft/           # Autonomous phase-gate execution (C → R → A → F → T → S)
└── craft-hitl/      # Same flow with mandatory human-in-the-loop gating at TODO(human) seams
```

### `/craft` — Autonomous CRAFTS Workflow

Invoke for every non-trivial task that can be completed without human judgment at a critical seam.

CRAFTS is a sequential phase-gate workflow: finish the current phase before moving to the next. Do not run phases in parallel.

**Full flow** (business logic, multi-file work, domain boundaries):

| Phase | Subagent | What happens |
|-------|----------|-------------|
| **C**onceptualize | `craft-planner` | Scope, test cases, implementation plan, risks before coding |
| **R**ender | `craft-builder` | TDD strictly: Red → Green → Refactor |
| **A**ssess | `craft-evaluator` | Review diff for quality, reuse, efficiency, type correctness |
| **F**ix | `craft-builder` | Address blocking issues from Assess |
| **T**ighten | `craft-security` | Security-hardening review of the diff |
| **S**harpen | `craft-sharpener` | Update docs with lessons learned; commit |

**Lite flow** (config, scaffolding, single-file fixes): **R**ender → **S**harpen.

Start lite, escalate to full if the task grows.

**Builder / Evaluator model diversity:** When exact per-spawn model selection is available, the R/F `craft-builder` and A `craft-evaluator` must run on different but equal-capability models. If only tier aliases are supported, both remain at `medium` and the phase report notes that exact diversity could not be enforced.

### `/craft-hitl` — Human-in-the-Loop CRAFTS Workflow

Invoke for issue slices labeled **HITL implementation** or **HITL design/review**, or any task where the PRD reserves a critical decision for human judgment.

Identical to `/craft`, but **R — Render** contains a mandatory pause. The agent scaffolds to the seam, leaves exactly one `TODO(human)` marker with specific context, then pauses. The human fills the critical logic; the agent resumes verification and completion.

**When to pause:**
- Issue labeled **HITL implementation** — agent scaffolds/tests, human owns critical logic
- Issue labeled **HITL design/review** — requires human taste/content/design review
- Subjective choice (naming, UX copy, algorithmic trade-off) explicitly reserved for human judgment

**When NOT to pause:** routine refactoring, clear-cut implementation, or decisions inferable from the PRD.

If a task starts as HITL but the human defers the decision back to the agent, switch to `/craft` and complete autonomously.

## Agents

```
agents/
├── craft-planner/     # C phase — scope, tests, risks, plan
├── craft-builder/     # R/F phases — test-driven implementation and fixes
├── craft-evaluator/   # A phase — diff review, simplification, verification
├── craft-security/    # T phase — security and trust-boundary review
└── craft-sharpener/   # S phase — durable docs, standards, learnings
```

These are primary agents with `priority` fields. They appear in the agent picker and can be chatted with directly. They are also spawned on-demand by the `/craft` and `/craft-hitl` skills via `AgentSpawn`.

## Installation

Copy skills and agents into your project's `.agents/` directory (or your agent's global path):

```bash
cp -r skills/craft      /path/to/your-project/.agents/skills/
cp -r skills/craft-hitl /path/to/your-project/.agents/skills/
cp -r agents/*          /path/to/your-project/.agents/agents/
```

Then invoke with `/craft` and `/craft-hitl` from your agent interface.

## Philosophy

**Planning beats replanning.** Wrong assumptions caught before code exists cost minutes. Caught after implementation they cost hours. CRAFTS front-loads the thinking in Conceptualize.

**Self-review is impossible.** The builder can't see their own blind spots. The Assess and Tighten phases are fresh-context reviews because the agent that wrote the code is the worst reviewer of it.

**Security is a gate, not an afterthought.** Tighten runs before every commit. Vulnerabilities found pre-commit cost minutes; found post-deploy they cost far more.

**Knowledge compounds.** Sharpen updates domain docs with lessons learned after every task. Week 1 those files are stubs. Week 8 they're handbooks that make every subsequent task faster and less error-prone.

**The right human at the right seam.** HITL gating isn't about distrusting the agent — it's about reserving human judgment for the decisions that actually need it, while the agent handles everything else autonomously.

## License

MIT
