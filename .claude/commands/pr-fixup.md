---
description: "Check a PR for merge conflicts, review comments, and CI failures — then fix them."
argument-hint: "[pr-number-or-url]"
---

You are performing a PR fixup. Your job is to identify and resolve everything blocking a PR from merging: conflicts, review feedback, and CI failures.

**Target PR:** $ARGUMENTS (if empty, detect from the current branch via `gh pr view`)

## Step 0: Identify the PR

- If `$ARGUMENTS` is provided, use it as the PR number or URL.
- Otherwise, run `gh pr view --json number,url,headRefName,baseRefName` to detect the current branch's PR.
- If no PR is found, stop and tell the user.
- Store the PR number, head branch, and base branch for use in subsequent steps.
- Make sure you are on the PR's head branch and it's up to date: `git checkout <head-branch> && git pull origin <head-branch>`

## Step 1: Check for merge conflicts

```bash
gh pr view <number> --json mergeable,mergeStateStatus
```

- If `mergeable` is `CONFLICTING`:
  1. Fetch the latest base branch: `git fetch origin <base>`
  2. Attempt a merge: `git merge origin/<base>`
  3. For each conflicted file, read the file, understand both sides, and resolve the conflict by preserving the intent of both changes. Prefer the PR's changes when the base branch change is unrelated to this PR's purpose.
  4. After resolving, run the project's quality checks (lint, type check, tests) to verify the resolution didn't break anything.
  5. Commit the merge resolution and push.
- If `mergeable` is `MERGEABLE` or `UNKNOWN`, note it and move on.

## Step 2: Check review comments and address them

Fetch all review comments:

```bash
gh api repos/{owner}/{repo}/pulls/<number>/comments --jq '.[] | "--- \(.path):\(.original_line // .line) [\(.user.login)] ---\n\(.body)\n"'
```

Also fetch review-level comments (top-level review bodies):

```bash
gh api repos/{owner}/{repo}/pulls/<number>/reviews --jq '.[] | select(.body != "") | "--- Review by \(.user.login) [\(.state)] ---\n\(.body)\n"'
```

For each comment:

1. **Read the relevant code** in the current branch to understand the context.
2. **Evaluate the feedback:**
   - Is it valid and actionable? Fix it.
   - Is it a style preference or subjective? Note it but skip unless it's from the repo owner.
   - Is it incorrect or based on a misunderstanding? Prepare a reply explaining why.
3. **For valid feedback:** Make the fix, ensuring it doesn't break tests or type checking.
4. **For feedback you disagree with:** Do NOT silently ignore it. Draft a reply comment explaining your reasoning.

After addressing all comments:
- Commit all fixes in a single commit with message: `Address PR review feedback`
- Push to the PR branch.

## Step 3: Check CI status and fix failures

```bash
gh pr checks <number>
```

For each failed check:

1. **Get the failure details:**
   ```bash
   gh run view <run-id> --job <job-id> --log-failed
   ```

2. **Diagnose the failure:**
   - **Lint/format failure**: Run the linter locally, apply auto-fixes, verify.
   - **Type check failure**: Run mypy on the affected files, fix type errors.
   - **Test failure**: Run the failing test locally, read the error, fix the root cause. Do NOT just make the test pass — fix the actual bug if there is one.
   - **Build/install failure**: Check dependency changes, lockfile issues.

3. **Verify the fix locally** by running the same check that failed.

4. **Commit and push** the fix.

## Step 4: Summary

After all three steps, output a summary:

```markdown
## PR Fixup Summary: #<number>

### Merge Conflicts
- [status: none / resolved / unresolvable]

### Review Comments
- [N comments addressed, M replies drafted, K skipped (with reasons)]

### CI Checks
- [status of each check: pass / fixed / still failing]

### Result
- [READY TO MERGE / NEEDS ATTENTION (explain what's left)]
```

## Rules

- **Read before fixing.** Always read the file and understand the context before making changes.
- **Run checks after each fix.** Don't accumulate fixes — verify after each step.
- **Don't over-fix.** Only address what's actually broken or requested. No "while I'm here" improvements.
- **Respect the project's conventions.** Read the relevant CLAUDE.md before touching domain code.
- **Be transparent.** If you can't fix something or disagree with feedback, say so clearly.
- **Never force-push.** Always push normally. If the branch has diverged, merge — don't rebase.
- **Commit messages matter.** Each fix commit should describe what it addresses.
