# CRAFTS — CLAUDE.md Drop-in

Copy this into your project's `CLAUDE.md` to enable the CRAFTS workflow.

---

```markdown
## Development Workflow

Follow CRAFTS for every non-trivial task.

**Full flow** — business logic, multiple files, domain boundaries: C → R → A → F → T → S
**Lite flow** — config, scaffolding, single-file fixes: R → S only
**Escalate** — start Lite, switch to Full if complexity grows

### C — Conceptualize
Use /plan. Produce scope, test cases, implementation plan, and risks.
Stop for human review before writing any code.

### R — Render (Test-Drive)
TDD is mandatory. No implementation before a failing test.
1. Red — write the failing test from the plan. If you can't write it, return to Conceptualize.
2. Green — write the minimum implementation to pass. No more.
3. Refactor — clean up without breaking green. Repeat for each test case.
Run lint, type checks, and format when all tests pass.

### A — Assess
Run /simplify on the diff. Address quality, reuse, and efficiency issues before moving on.

### F — Fix
Address blocking issues from Assess. Re-run quality checks.
Disagree with a finding? Document why instead of blindly fixing.

### T — Tighten
Run the security-scanning-security-hardening skill on the diff.
Fix all findings before proceeding.

### S — Sharpen
Commit and push. Update the relevant domain CLAUDE.md with lessons learned:
patterns established, gotchas discovered, conventions set during this task.
```

---

## Phase Reference

### C — Conceptualize
**Tool:** `/plan` (built-in)
Use Claude Code's planning agent. It reads project context and produces scope, test cases, an implementation plan, and risks. The plan's test cases are the input to Render — Conceptualize writes them on paper, Render makes them pass in code.

### R — Render (Test-Drive)
**Tool:** Main agent
TDD is the method, not a suggestion. Red → Green → Refactor per test case from the plan. If a test case can't be written, the requirement isn't understood — return to Conceptualize.

### A — Assess
**Tool:** `/simplify` (built-in)
Claude Code's `/simplify` skill reviews the diff for reuse opportunities, code quality, efficiency, and naming. More thorough than a single custom reviewer agent.

### F — Fix
**Tool:** Main agent
Address what Assess surfaces. High and medium severity first. The review is advisory — document disagreements rather than applying every suggestion blindly.

### T — Tighten
**Tool:** `security-scanning-security-hardening` skill
Four-phase security hardening: vulnerability scanning, remediation, controls implementation, compliance validation. Runs against the diff before commit.

### S — Sharpen
**Tool:** Main agent
Commit, push, update documentation. Each domain's CLAUDE.md grows from a stub into a real handbook through this phase — patterns, gotchas, and conventions compound over time.
