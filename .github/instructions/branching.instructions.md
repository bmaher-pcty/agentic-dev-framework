---
applyTo: "**/*"
---

# Branching Workflow Instructions

Use these instructions at the start of every new work session.

## Session Start Protocol

1. Run `git status` to confirm current branch and working tree state.
2. If starting new work, sync to latest main before branching:
   ```bash
   git checkout main
   git pull origin main --ff-only
   git checkout -b <type>/<short-description>
   ```
3. If resuming existing work, confirm the branch is current with main:
   ```bash
   git fetch origin
   git --no-pager log --oneline HEAD..origin/main | head -5
   ```
   If main has moved ahead, rebase or merge before continuing.

## Branch Naming

Format: `<type>/<short-description>`

- `feat/` — new features.
- `fix/` — bug fixes.
- `refactor/` — restructuring without behavior change.
- `docs/` — documentation only.
- `test/` — test additions or improvements.
- `chore/` — tooling, CI, dependencies.

Keep descriptions kebab-case, concise, and descriptive (e.g., `feat/user-auth-flow`).

## Phase / Task Completion Gate

A phase or task is **not complete** until:
1. All deliverables are committed to the feature branch.
2. The branch is pushed to `origin`.
3. A pull request is open on GitHub (`gh pr create` or equivalent).

Do not declare a phase done, summarize it as finished, or move on to the next phase until the PR URL has been confirmed. "Implementation complete" without an open PR is not done.

## Rules

1. Never commit directly to `main`.
2. Never start new feature work on a stale or unrelated branch.
3. One logical change per branch.
4. Pull latest `main` before creating any new branch.
5. Delete merged branches to keep the branch list clean.
6. Every completed phase ends with an open PR — no exceptions.
