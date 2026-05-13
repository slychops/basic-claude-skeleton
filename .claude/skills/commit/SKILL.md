---
name: commit
description: Reviews changes, commits, and opens a GitHub PR.
argument-hint: [commit_message_suggestion]
parameters:
  message:
    type: string
    description: Suggested commit message (will be confirmed with user)
---

# Commit & PR Skill

**Purpose:** This skill runs the standard end-of-task flow: safety checks → code review → commit → PR. It encodes the project's quality gate so every committed change has been reviewed before it leaves your machine.

**How you use this skill:** Invoke it as `/commit "<message suggestion>"`. The message is a hint — you will be asked to confirm the final wording before the commit is created. Decline the final gate if anything looks off.

**How Claude should use this skill:** Run the steps in order. Do not skip the *Branch Guard* — committing to the default branch is forbidden. Do not skip the *Code Review* — the `review-pr` skill must pass (or be explicitly overridden) before the commit lands. Stop and ask the user at the *Final Gate* before any `git commit` runs.

---

## 1. Safety & Pre-flight

- **Branch Guard:** Identify the default branch via `git remote show origin`. If on the default branch, abort and ask the user to create a feature branch first.
- **Leak Prevention:** Scan `git diff --cached` and `git diff` for `KEY`, `SECRET`, `PASSWORD`, `TOKEN`, `BEGIN PRIVATE`. If any match, alert the user and STOP.
- **Cleanliness:** Run `git status`. Confirm no untracked files were forgotten — surface anything that looks accidentally excluded.

## 2. Documentation Sync (Ground-Truth Check)

Before touching files:

- Compare the conversation history against `git status` and `git diff --stat`.
- Documentation MUST reflect the files actually modified, not what was planned.
- If `CLAUDE.md`, an agent doc, or a feature spec describes work that wasn't done, ask the user how to handle it before continuing.

## 3. Code Review

Run the `review-pr` skill on the current diff:

```
/review-pr all
```

- **Critical issues found** → present them to the user. Offer to fix before proceeding. After fixes, re-run this step. Loop until no Critical Issues remain or the user explicitly overrides.
- Store the final review output (Critical, Important, Suggestions, Strengths) — it will be posted as a PR comment after creation.

## 4. The Final Gate (Interactive)

Present the following to the user before executing `git commit`:

1. **Branch name** being pushed.
2. **Commit message** in imperative mood (e.g. `feat(player): add coyote-time jump buffer`).
3. **Review summary** — counts of Critical / Important / Suggestion findings.
4. **Action:** "Awaiting 'yes' to proceed with commit and PR creation."

Do not proceed without an affirmative response.

## 5. Execution

Once the user confirms:

```bash
git add -A
git commit -m "<message>

Co-Authored-By: Claude <noreply@anthropic.com>"
git push -u origin $(git branch --show-current)
gh pr create --title "<message>" --body "## Summary
<bullets>

## Test Plan
- <verification steps>

## Review
- Critical: N | Important: N | Suggestions: N"
```

After the PR is created, post the full `review-pr` output as a PR comment:

```bash
gh pr review --comment -b "<formatted review output from step 3>"
```

## 6. Report

Return to the user:

- Commit SHA
- PR URL
- Summary of any deferred follow-ups (e.g. "Critical issue X waived — tracked as TODO in code")

## Notes

- This skill never bypasses the review step. If you find yourself wanting to skip it, the right answer is to fix the review findings first, not to skip the gate.
- Conventional commit prefixes are encouraged: `feat(domain)`, `fix(core)`, `docs(style)`, `refactor`, `test`, `chore`.
- Multi-line bash commands are forbidden — always single-line invocations.
