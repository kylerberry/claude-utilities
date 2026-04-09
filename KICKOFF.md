# KICKOFF: From Idea to Executing CRAFTS

A repeatable process for taking a raw idea to a properly scaffolded project — before you write a single line of code.

---

## Why This Matters

Most projects fail in the first hour — not because of bad code, but because of bad thinking. Jumping straight from idea to implementation skips the steps that surface wrong assumptions, missing requirements, and architectural decisions that are cheap to change on paper and expensive to change in code.

KICKOFF is the process that runs before CRAFTS begins.

---

## The Steps

### 1. Riff the Idea (Proposal v1)

**Tool:** Claude or Gemini (conversational, unconstrained)

Don't start with structure — start with thinking. Dump your raw idea into a conversation and let the agent help you develop it into a coherent v1 proposal. Push for:
- What problem does this actually solve?
- Who is it for?
- What does success look like?
- What are the rough edges?

Save the output as `specs/proposal.md`.

**Goal:** A rough but honest first draft — not polished, not complete. Good enough to red-team.

---

### 2. Red-Team the Proposal

**Tool:** A different agent than Step 1 (switch Claude → Gemini or vice versa)

Fresh model, no prior context. Hand it the proposal and ask it to attack it:
- What assumptions are unvalidated?
- What's missing?
- What could go wrong?
- What would a skeptical stakeholder push back on?

Use the critique to update `specs/proposal.md`.

**Goal:** A proposal that has survived adversarial review. Weaknesses surfaced now cost nothing.

---

### 3. Refine with the Original Agent

**Tool:** Return to your Step 1 agent

Hand it both the updated proposal and the red-team critique. Ask it to reconcile tensions, fill gaps, and produce a stronger version.

Update `specs/proposal.md` again.

**Goal:** A proposal that incorporates both generative thinking and adversarial feedback. This is your source of truth going forward.

---

### 4. Generate the Product Spec

**Tool:** Claude

Take the refined proposal and ask Claude to produce a thorough product spec. The spec should include:
- Problem statement and goals
- User stories or jobs-to-be-done
- Feature list with scope boundaries (in/out)
- Non-functional requirements (performance, security, scale)
- Open questions and decisions still needed

Save as `specs/spec.md`.

**Goal:** A spec detailed enough that a developer (or agent) could build from it without needing to invent requirements.

---

### 5. Discover and Map Skills

**Tool:** Claude + `skills.sh` + `find-skills` skill

Using the spec as input, ask Claude to identify all skills relevant to this project. For each skill found:
- **Reasoning** — why this skill is relevant
- **Trust factor** — how much to rely on it (community vs. verified, complexity, risk)
- **Domain mapping** — which part of the future codebase this skill serves

Save the output as `specs/skill-map.md`.

**Example skill-map entry:**
```markdown
## security-scanning-security-hardening
- **Relevance:** All backend endpoints handle user data; security hardening required before each commit
- **Trust:** Community skill, well-documented, 4-phase process — high trust
- **Domain:** `app/api/`, `app/auth/`
```

**Goal:** Know exactly which skills you're bringing in, why, and where they apply — before installing anything.

---

### 6. Initialize CLAUDE.md and Install Skills

**Tool:** Claude

With the spec and skill-map in hand:

1. Ask Claude to scaffold the project's root `CLAUDE.md` — tech stack, architecture invariants, domain structure, CRAFTS workflow reference
2. Create domain-level `CLAUDE.md` stubs for each planned domain
3. Install skills from `specs/skill-map.md`

```bash
# Copy skills from claude-utilities or install individually
cp -r /path/to/claude-utilities/.claude/skills/ .claude/skills/
```

**Goal:** A project that Claude can navigate from the first conversation — with the right context at every level.

---

### 7. Generate the Task List

**Tool:** Claude Opus (most capable — use it here)

Using the spec, hand Claude Opus a prompt like:

> "Using specs/spec.md, generate a thorough, phased task list for this project. Organize tasks by phase (foundation → core features → polish → launch). Each task should be concrete enough to execute in a single CRAFTS session. Save to specs/tasks.md."

The task list persists across sessions — it's your single source of truth for what's done and what's next.

`specs/tasks.md` structure:
```markdown
## Phase 1: Foundation
- [ ] Set up database schema and migrations
- [ ] Scaffold auth system
- [ ] Configure CI/CD pipeline

## Phase 2: Core Features
- [ ] Build X
- [ ] Build Y
...
```

**Goal:** A phased, checkable task list that survives context resets and keeps work moving forward without re-planning.

---

### 8. Execute with CRAFTS

**Tool:** Claude + CRAFTS workflow

Pick the first unchecked task from `specs/tasks.md` and run it through the full CRAFTS flow:

**Conceptualize → Render → Assess → Fix → Tighten → Sharpen**

Check off tasks as they complete. Update domain `CLAUDE.md` files during the Sharpen phase.

See [CRAFTS.md](CRAFTS.md) for the full workflow.

---

## Output Checklist

Before starting Step 8, you should have:

- [ ] `specs/proposal.md` — refined, red-teamed proposal
- [ ] `specs/spec.md` — thorough product spec
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
Riff (Step 1) → Proposal v1
   ↓
Red-Team (Step 2) → Critique
   ↓
Refine (Step 3) → Proposal v2
   ↓
Spec (Step 4) → specs/spec.md
   ↓
Skill Discovery (Step 5) → specs/skill-map.md
   ↓
Init (Step 6) → CLAUDE.md + skills installed
   ↓
Task List (Step 7) → specs/tasks.md
   ↓
CRAFTS Execution (Step 8) →  Shipped software
```
