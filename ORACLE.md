# ORACLE — Project Kickoff Prompt Template

Paste this into a new conversation with Claude (or your preferred LLM) to kick off a project using the ORACLE process.

---

```
You are helping me take a raw idea through the ORACLE process — a structured
kickoff that produces a product spec, skill map, and task list before any code
is written.

The phases are:
  O — Originate    (we do this now, together)
  R — Red-Team     (I'll switch to a different LLM and return with the critique)
  A — Amend        (we reconcile the critique and harden the proposal)
  C — Constitution (you produce a thorough product spec)
  L — Layout       (you map the skills and domains the project needs)
  E — Execute      (you init CLAUDE.md, generate a task list, and we begin CRAFTS)

Work through one phase at a time. Wait for me to confirm before moving to the next.

---

O — ORIGINATE

Help me develop this idea into a v1 proposal. Ask clarifying questions, push
back on vague assumptions, and help me think through:
- What problem does this actually solve?
- Who is it for?
- What does success look like in 90 days?
- What are the obvious risks or rough edges?

When we're satisfied, save the output to specs/proposal.md.

---

R — RED-TEAM

I will now switch to a different LLM (e.g. Gemini if we started with Claude,
or vice versa) and ask it to adversarially review the proposal. I'll return
with its critique.

(Pause — I'll be back with the red-team output.)

---

A — AMEND

I'm back. Here is the red-team critique: [PASTE CRITIQUE]

Reconcile the tensions, fill the gaps, and produce a stronger version of the
proposal. Update specs/proposal.md. We can repeat R → A as many times as
needed until the proposal holds up.

---

C — CONSTITUTION

Using the hardened proposal, produce a thorough product spec. Include:
- Problem statement and goals
- User stories or jobs-to-be-done
- Feature list with explicit in/out scope
- Non-functional requirements (performance, security, scale)
- Open questions and decisions still needed

Save to specs/spec.md. This is the law of the project — everything we build
will reference it.

---

L — LAYOUT

Using specs/spec.md, identify all relevant Claude Code skills for this project
using the find-skills skill. For each skill:
- Reasoning — why it's relevant to this project
- Trust factor — community vs. verified, complexity, risk level
- Domain mapping — which part of the future codebase it serves

Save to specs/skill-map.md.

---

E — EXECUTE

1. Scaffold the root CLAUDE.md with: tech stack, architecture invariants,
   domain structure, and the CRAFTS workflow.
2. Create domain-level CLAUDE.md stubs for each planned domain.
3. Install skills from specs/skill-map.md.
4. Using specs/spec.md, generate a thorough phased task list with Claude Opus.
   Organize by phase: foundation → core features → polish → launch.
   Each task should be completable in a single CRAFTS session.
   Save to specs/tasks.md.
5. Pick the first task from specs/tasks.md and begin CRAFTS.

---

My idea: [YOUR IDEA HERE]
```

---

## Output Checklist

Before CRAFTS begins, you should have:

- [ ] `specs/proposal.md` — refined, red-teamed proposal
- [ ] `specs/spec.md` — product spec (the Constitution)
- [ ] `specs/skill-map.md` — skills mapped to domains with trust ratings
- [ ] `specs/tasks.md` — phased task list
- [ ] `CLAUDE.md` — root project context initialized
- [ ] Domain `CLAUDE.md` stubs created
- [ ] Skills installed
