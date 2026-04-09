# CRAFTS: A Development Workflow for AI-Assisted Coding

**Conceptualize > Render > Assess > Fix > Tighten > Sharpen**

CRAFTS is a structured workflow for building software with AI coding agents. It prevents the most common failure mode — jumping straight to code and discovering problems late — by enforcing deliberate phases with clear handoffs.

---

## The Phases

### C — Conceptualize (Plan)

**Tool:** Claude Code's built-in `Plan` agent (`/plan`)
**Purpose:** Understand the task deeply before writing a single line of code.

Claude Code ships with a capable planning agent. Use it to produce:
- **Scope** — what's in, what's out, what's ambiguous
- **Test cases** — the specific tests to write first (TDD)
- **Implementation plan** — files to create/modify, key decisions, dependencies
- **Risks** — edge cases, boundary violations, spec gaps

**Why this matters:** Planning catches misunderstandings when they're free to fix. A 5-minute plan prevents a 30-minute rewrite.

**When to skip:** Config changes, scaffolding, obvious single-file fixes. If you can hold the entire change in your head, you don't need a plan.

### R — Render (Test-Drive)

**Tool:** Main agent (you)
**Purpose:** Turn the plan's test cases into passing code — no implementation before a failing test.

Conceptualize hands you test cases on paper. Render makes them real:

1. **Red** — Write the failing test from the plan. If you can't write the test, go back to Conceptualize — you don't understand the requirement yet.
2. **Green** — Write the minimum implementation to make it pass. No more.
3. **Refactor** — Clean up without breaking green. Then repeat for the next test case.
4. When all test cases pass: run lint, type checks, and format before moving on.

TDD is not optional in this phase. Backfilling tests after implementation defeats the purpose — the test *is* the spec.

### A — Assess (Review)

**Tool:** `/simplify`
**Purpose:** Fresh eyes on the code before it leaves your machine.

Claude Code's `/simplify` skill is a more thorough reviewer than any single custom agent — it evaluates the diff for:
- Reuse opportunities and over-engineering
- Code quality, efficiency, and naming
- Clarity and simplicity

It produces either an approval or a list of issues to address.

**Why this matters:** You can't proofread your own work. `/simplify` provides a fresh-context review pass that catches what the builder is blind to — before the code reaches PR review.

### F — Fix

**Tool:** Main agent (you)
**Purpose:** Address blocking issues from the review.

For each issue raised:
1. Read the reasoning
2. Fix the issue
3. Re-run quality checks

Don't blindly fix everything — if you disagree, document why. The review is advisory, not authoritative.

### T — Tighten (Security Hardening)

**Skill:** `security-scanning-security-hardening`
**Purpose:** Harden the diff against security vulnerabilities before commit.

Run the security hardening skill against the previous diff. It scans for:
- Injection vulnerabilities (SQL, command, XSS)
- Credential and secret exposure
- Insecure dependencies or patterns
- OWASP Top 10 concerns

Address all findings before proceeding.

**Why this matters:** Security issues caught pre-commit cost minutes to fix. The same issues caught post-deploy cost days, reputation, and sometimes data.

### S — Sharpen (Improve)

**Tool:** Main agent (you)
**Purpose:** Capture institutional knowledge so the next task is easier.

After the task is complete, commit, and push:
1. Update the relevant domain's documentation with lessons learned
2. Focus on things that aren't obvious from the code:
   - Problems encountered and their solutions
   - Conventions established during implementation
   - Gotchas that would waste time if rediscovered

**Why this matters:** Without this phase, every task starts from scratch. The Sharpen phase builds a knowledge base that compounds over time. Each domain's documentation evolves from a placeholder into real institutional knowledge.

---

## Project Structure: Domain-Driven Design with Cascading CLAUDE.md

CRAFTS isn't just a workflow — it assumes a specific project structure that makes AI agents dramatically more effective. The key idea: **organize code by domain, and give each domain its own CLAUDE.md.**

### Why This Matters for AI

An AI agent's biggest limitation is context. When you dump an entire codebase into a conversation, the agent drowns in irrelevant details. Domain-driven structure solves this:

- The **root CLAUDE.md** gives the agent the big picture — architecture invariants, code standards, project conventions
- The **domain CLAUDE.md** gives the agent the local picture — what this module does, what patterns it uses, what lessons have been learned here
- The agent reads both, and now it has exactly the context it needs — no more, no less

### The Structure

```
project/
├── CLAUDE.md                    # Root: architecture, invariants, workflow, standards
├── specs/                       # Design docs, specs, decision logs
├── app/
│   ├── core/                    # Shared infrastructure (models, config, DB, etc.)
│   │   └── CLAUDE.md            # Core conventions, available utilities
│   ├── domain-a/
│   │   └── CLAUDE.md            # Domain A scope, patterns, lessons learned
│   ├── domain-b/
│   │   └── CLAUDE.md            # Domain B scope, patterns, lessons learned
│   └── domain-c/
│       └── CLAUDE.md            # Domain C scope, patterns, lessons learned
└── tests/
```

### What Goes Where

**Root CLAUDE.md** — the constitution. Things that apply everywhere:
- Tech stack and key dependencies
- Architecture invariants (non-negotiable rules)
- Development workflow (CRAFTS phases)
- Code standards (formatting, naming, testing conventions)
- Project structure overview
- CI/CD pipeline description

**Domain CLAUDE.md** — the local handbook. Things specific to this module:
- What the domain owns and its boundaries
- Implemented features and their signatures
- Patterns and conventions established during implementation
- Lessons learned — gotchas, surprising behavior, non-obvious edge cases
- Dependencies on other domains (always through `core/`)
- Relevant skills or reference material

### The Cascade

When an agent works on a file in `domain-b/`, it reads:

1. **Root CLAUDE.md** → "Here are the project-wide rules"
2. **domain-b/CLAUDE.md** → "Here's what's specific to this area"

This cascading context is what makes the Sharpen phase so powerful. Every time you complete a task and update the domain's CLAUDE.md, you're teaching future agents (and future you) what they need to know. The documentation compounds:

- **Week 1:** Domain CLAUDE.md says "TODO: write after implementation"
- **Week 4:** Domain CLAUDE.md has patterns, gotchas, and conventions from 3 completed tasks
- **Week 8:** Domain CLAUDE.md is a genuine handbook that prevents repeated mistakes

### Architecture Invariants

The root CLAUDE.md should include a section of non-negotiable rules — things that, if violated, indicate a structural problem. Examples:

```markdown
## Architecture Invariants

These are non-negotiable. If a change violates one, stop and reconsider.

1. All DB queries filter by tenant_id. No exceptions.
2. All external API calls go through adapters. No direct SDK usage in business logic.
3. Cross-domain imports go through core/. Never import from one domain into another.
4. Credentials are never logged, cached, or stored in plaintext.
```

The planning phase checks these during Conceptualize. The security hardening skill checks them during Tighten. They're the guardrails that keep parallel work from creating architectural drift.

### Domain Boundaries

The golden rule: **domains don't import from each other.** If domain A needs something from domain B, it goes through `core/`. This means:

- Domains can be developed in parallel without merge conflicts
- Each domain can be understood in isolation
- Boundary violations can be caught mechanically during review

If you find yourself wanting to import across domains, that's a signal the shared code belongs in `core/`.

---

## When to Use Full vs Lite CRAFTS

| Scenario | Flow |
|----------|------|
| Business logic, multiple files, domain boundaries | Full: **C → R → A → F → T → S** |
| Config changes, scaffolding, single-file fixes | Lite: **R → S** (skip Conceptualize, Assess, Fix, Tighten) |
| Uncertain complexity | Start Lite, escalate to Full if it gets non-trivial |

---

## Setting Up CRAFTS in Your Project

### 1. Copy the security hardening skill

```bash
cp -r .claude/skills/ /path/to/your-project/.claude/skills/
```

### 2. Add the workflow to your CLAUDE.md

Add this to your project's `CLAUDE.md` to instruct Claude to follow CRAFTS:

```markdown
## Development Workflow

Every non-trivial task follows the CRAFTS workflow:

1. **Conceptualize** — Use `/plan` to produce scope, test cases, implementation plan, and risks. Human reviews before proceeding.
2. **Render** — Implement per TDD (tests first, then implementation). Lint and type check before moving on.
3. **Assess** — Run `/simplify` on the changed code. Address quality issues.
4. **Fix** — Address any blocking issues from the review.
5. **Tighten** — Run `security-scanning-security-hardening` skill on the diff. Fix all findings.
6. **Sharpen** — Commit, push, and update documentation with lessons learned.

**Full flow:** Tasks touching business logic, multiple files, or domain boundaries.
**Lite flow:** Config changes, scaffolding, obvious single-file fixes. Skip C, A, F, T.
```

### 3. Optionally add the `/pr-fixup` command

For post-push cleanup (merge conflicts, review comments, CI failures):

```bash
cp .claude/commands/pr-fixup.md /path/to/your-project/.claude/commands/
```

---

## Philosophy

CRAFTS is built on five beliefs:

1. **Planning is not overhead.** It's the cheapest place to catch mistakes. The cost of a wrong plan is minutes; the cost of wrong code is hours.

2. **Self-review is impossible.** You can't see your own blind spots. A fresh review pass — via `/simplify` — catches what the builder misses. A robust built-in skill beats a hand-rolled single-purpose agent.

3. **Security is not a phase you add later.** The Tighten phase makes security hardening a non-negotiable gate, not an afterthought. Vulnerabilities caught pre-commit cost minutes; caught post-deploy they cost far more.

4. **Context is the bottleneck.** AI agents are only as good as the context they receive. Domain-driven structure with cascading CLAUDE.md files gives agents precisely the context they need — project-wide rules from the root, domain-specific knowledge from the local file. Without this structure, agents either drown in irrelevant details or miss critical constraints.

5. **Knowledge compounds.** Every task should leave the codebase slightly smarter than it found it. The Sharpen phase is what separates a codebase that fights you from one that helps you. Each domain's CLAUDE.md evolves from a placeholder into real institutional knowledge — and that knowledge makes every subsequent task faster and less error-prone.
