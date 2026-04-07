# CRAFT: A Development Workflow for AI-Assisted Coding

**Conceptualize > Render > Assess > Fix > Transform**

CRAFT is a structured workflow for building software with AI coding agents. It prevents the most common failure mode — jumping straight to code and discovering problems late — by enforcing deliberate phases with clear handoffs.

---

## The Phases

### C — Conceptualize (Plan)

**Agent:** `planner`
**Purpose:** Understand the task deeply before writing a single line of code.

The planner agent reads the project context (specs, CLAUDE.md, existing code) and produces:
- **Scope** — what's in, what's out, what's ambiguous
- **Test cases** — the specific tests to write first (TDD)
- **Implementation plan** — files to create/modify, key decisions, dependencies
- **Risks** — edge cases, boundary violations, spec gaps

**Why this matters:** Planning catches misunderstandings when they're free to fix. A 5-minute plan prevents a 30-minute rewrite.

**When to skip:** Config changes, scaffolding, obvious single-file fixes. If you can hold the entire change in your head, you don't need a plan.

### R — Render (Build)

**Agent:** Main agent (you)
**Purpose:** Write tests first, then implement.

Follow TDD strictly:
1. Write a failing test that describes expected behavior
2. Write the minimum implementation to make it pass
3. Iterate — tests and implementation grow together
4. Run lint, format, and type checks before moving on

**Key principle:** Don't write the implementation first and backfill tests. The test is the spec — if you can't write the test, you don't understand the requirement.

### A — Assess (Review)

**Agent:** `reviewer`
**Purpose:** Fresh eyes on the code before it leaves your machine.

The reviewer agent reads the diff with no prior context and checks:
- Spec compliance
- Architecture invariant violations
- Test quality and coverage
- Code quality and naming
- Scope creep

It produces either an approval or a list of issues with severity ratings.

**Why this matters:** The reviewer catches things the builder is blind to — you can't proofread your own work. Catching issues before commit is cheaper than catching them in PR review.

### F — Fix

**Agent:** Main agent (you)
**Purpose:** Address blocking issues from the review.

For each issue flagged as high or medium severity:
1. Read the reviewer's reasoning
2. Fix the issue
3. Re-run quality checks

Don't blindly fix everything — if you disagree with the reviewer, document why. The reviewer is advisory, not authoritative.

### T — Transform (Improve)

**Agent:** Main agent (you)
**Purpose:** Capture institutional knowledge so the next task is easier.

After the task is complete, commit, and push:
1. Update the relevant domain's documentation with lessons learned
2. Focus on things that aren't obvious from the code:
   - Problems encountered and their solutions
   - Conventions established during implementation
   - Gotchas that would waste time if rediscovered

**Why this matters:** Without this phase, every task starts from scratch. The Transform phase builds a knowledge base that compounds over time. Each domain's documentation evolves from a placeholder into real institutional knowledge.

---

## When to Use Full vs Lite CRAFT

| Scenario | Flow |
|----------|------|
| Business logic, multiple files, domain boundaries | Full: **C → R → A → F → T** |
| Config changes, scaffolding, single-file fixes | Lite: **R → T** (skip Conceptualize, Assess, Fix) |
| Uncertain complexity | Start Lite, escalate to Full if it gets non-trivial |

---

## Setting Up CRAFT in Your Project

### 1. Copy the agents

```bash
cp -r .claude/agents/ /path/to/your-project/.claude/agents/
```

### 2. Add the workflow to your CLAUDE.md

Add this to your project's `CLAUDE.md` to instruct Claude to follow CRAFT:

```markdown
## Development Workflow

Every non-trivial task follows the CRAFT workflow:

1. **Conceptualize** — Invoke the `planner` sub-agent. Human reviews the plan before proceeding.
2. **Render** — Implement per TDD (tests first, then implementation). Lint and type check before moving on.
3. **Assess** — Invoke the `reviewer` sub-agent. It checks the diff for issues.
4. **Fix** — Address any blocking review issues.
5. **Transform** — Commit, push, and update documentation with lessons learned.

**Full flow:** Tasks touching business logic, multiple files, or domain boundaries.
**Lite flow:** Config changes, scaffolding, obvious single-file fixes. Skip C, A, F.
```

### 3. Optionally add the `/pr-fixup` command

For post-push cleanup (merge conflicts, review comments, CI failures):

```bash
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

---

## Philosophy

CRAFT is built on three beliefs:

1. **Planning is not overhead.** It's the cheapest place to catch mistakes. The cost of a wrong plan is minutes; the cost of wrong code is hours.

2. **Self-review is impossible.** You can't see your own blind spots. A separate review agent with fresh context catches what you miss.

3. **Knowledge compounds.** Every task should leave the codebase slightly smarter than it found it. The Transform phase is what separates a codebase that fights you from one that helps you.
