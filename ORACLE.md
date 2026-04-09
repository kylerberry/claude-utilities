# ORACLE: From Idea to Executing CRAFTS

**Originate > Red-Team > Amend > Constitution > Layout > Execute**

A repeatable process for taking a raw idea all the way through to shipping — idea to spec to scaffolded project to running CRAFTS.

---

## Why This Matters

Most projects fail in the first hour — not because of bad code, but because of bad thinking. Jumping straight from idea to implementation skips the steps that surface wrong assumptions, missing requirements, and architectural decisions that are cheap to change on paper and expensive to change in code.

ORACLE doesn't hand off to CRAFTS — it ends with CRAFTS already running.

---

## The Phases

### O — Originate

**Tool:** Claude or Gemini (conversational, unconstrained)
**Purpose:** Spark the genesis of the idea — active co-creation, not passive transcription.

Don't start with structure — start with thinking. Bring your raw idea into a conversation and push the agent to help you develop it into a coherent v1 proposal:
- What problem does this actually solve?
- Who is it for?
- What does success look like?
- What are the rough edges?

Save the output as `specs/proposal.md`.

**Goal:** A rough but honest first draft — not polished, not complete. Good enough to red-team.

---

### R — Red-Team

**Tool:** A different agent than Originate (switch Claude → Gemini or vice versa)
**Purpose:** Stress-test the proposal with an adversarial model that has no stake in the idea.

Fresh model, no prior context. Hand it the proposal and ask it to attack it:
- What assumptions are unvalidated?
- What's missing?
- What could go wrong?
- What would a skeptical stakeholder push back on?

Use the critique to update `specs/proposal.md`.

**Goal:** A proposal that has survived adversarial review. Weaknesses surfaced now cost nothing.

---

### A — Amend

**Tool:** Return to your Originate agent
**Purpose:** Close the loop — reconcile the critique and polish the concept.

Hand it both the updated proposal and the red-team critique. Ask it to reconcile tensions, fill gaps, and produce a stronger version. Repeat the R → A loop as many times as needed until the proposal holds up.

Update `specs/proposal.md`.

**Goal:** A proposal hardened by both generative thinking and adversarial feedback. This becomes your source of truth.

---

### C — Constitution

**Tool:** Claude
**Purpose:** Turn the proposal into the law of the project — the rules, scope, and requirements that everything else is built on.

Ask Claude to produce a thorough product spec from the refined proposal:
- Problem statement and goals
- User stories or jobs-to-be-done
- Feature list with scope boundaries (in/out)
- Non-functional requirements (performance, security, scale)
- Open questions and decisions still needed

Save as `specs/spec.md`.

**Goal:** A spec detailed enough that a developer (or agent) could build from it without inventing requirements.

---

### L — Layout

**Tool:** Claude + `skills.sh` + `find-skills` skill
**Purpose:** Map the domains and stack — visually and structurally lay out what the project needs and where.

Using the spec as input, ask Claude to identify all relevant skills and map them to the domains of the future codebase. For each skill:
- **Reasoning** — why this skill is relevant
- **Trust factor** — community vs. verified, complexity, risk
- **Domain mapping** — which part of the codebase this skill serves

Save as `specs/skill-map.md`.

**Example entry:**
```markdown
## security-scanning-security-hardening
- **Relevance:** All backend endpoints handle user data; hardening required before each commit
- **Trust:** Community skill, well-documented, 4-phase process — high trust
- **Domain:** `app/api/`, `app/auth/`
```

**Goal:** Know exactly which skills you're bringing in, why, and where they apply — before installing anything.

---

### E — Execute

**Tool:** Claude (init) + Claude Opus (task list) + CRAFTS
**Purpose:** Initialize the environment, build the task list, and start shipping — Execute is where ORACLE ends and CRAFTS begins running.

**1. Initialize the project context**

Ask Claude to scaffold the root `CLAUDE.md` — tech stack, architecture invariants, domain structure, CRAFTS workflow reference. Create domain-level `CLAUDE.md` stubs for each planned domain. Install skills from `specs/skill-map.md`:

```bash
cp -r /path/to/claude-utilities/.claude/skills/ .claude/skills/
```

**2. Generate the task list (use Opus)**

Hand Claude Opus the spec and ask it to produce a thorough, phased task list:

> "Using specs/spec.md, generate a thorough, phased task list for this project. Organize by phase (foundation → core features → polish → launch). Each task should be concrete enough to execute in a single CRAFTS session. Save to specs/tasks.md."

```markdown
## Phase 1: Foundation
- [ ] Set up database schema and migrations
- [ ] Scaffold auth system
- [ ] Configure CI/CD pipeline

## Phase 2: Core Features
- [ ] Build X
- [ ] Build Y
```

**3. Run CRAFTS**

Pick the first unchecked task from `specs/tasks.md` and execute it through the full CRAFTS flow:

**Conceptualize → Render → Assess → Fix → Tighten → Sharpen**

Check off tasks as they complete. Update domain `CLAUDE.md` files during the Sharpen phase. Repeat until shipped.

See [CRAFTS.md](CRAFTS.md) for the full workflow.

**Goal:** A running project — not a ready-to-start one.

---

## Output Checklist

Before handing off to CRAFTS, you should have:

- [ ] `specs/proposal.md` — refined, red-teamed proposal
- [ ] `specs/spec.md` — product spec (the Constitution)
- [ ] `specs/skill-map.md` — skills mapped to domains with trust ratings
- [ ] `specs/tasks.md` — phased task list (generated by Opus)
- [ ] `CLAUDE.md` — root project context initialized
- [ ] Domain `CLAUDE.md` stubs created
- [ ] Skills installed

---

## The Full Arc

```
Raw Idea
   ↓
Originate     →  specs/proposal.md (v1)
   ↓
Red-Team      →  Adversarial critique
   ↓
Amend         →  specs/proposal.md (hardened)
   ↓
Constitution  →  specs/spec.md
   ↓
Layout        →  specs/skill-map.md
   ↓
Execute       →  CLAUDE.md + skills + specs/tasks.md + CRAFTS running
   ↓
              →  Shipped software
```
