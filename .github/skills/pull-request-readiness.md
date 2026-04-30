---
name: pull-request-readiness
description: 'Pre-PR quality gates for branch sync, commit quality, verification evidence, and test completion before push/opening a pull request.'
---

# Pull Request Readiness

## When to Use
- Any time code will be pushed or a pull request will be opened/updated.

## Required Sequence
1. Sync your branch with the latest `main` before opening or updating a PR.
2. Resolve merge conflicts locally and re-run verification after conflict resolution.
3. Verify the user-visible workflow manually (or closest realistic runtime path) before pushing.
4. Run all required automated tests before pushing.
5. Prepare clear commit messages that explain intent and impact.
6. Open PR with a descriptive title and detailed body including verification evidence.

## Commit Best Practices
- Use `type(scope): description` format.
- Keep commits focused and logically grouped.
- Explain why the change exists and what behavior changed.
- Avoid vague messages such as `fix stuff` or `updates`.

## PR Title Best Practices
- Be specific and outcome-oriented.
- Include scope and user/system impact.
- Prefer explicit titles such as `feat(reporting): persist report executions and surface latest results in UI`.

## PR Description Minimum Content
- Problem being solved and context.
- What changed (backend, frontend, data model, docs, security).
- Verification steps and observed outcomes.
- Tests executed and pass/fail summary.
- Known limitations, blockers, or follow-up tasks.

## Pre-Push / Pre-PR Gate
- Latest `main` merged or rebased into your branch.
- Functionality verified in the real user path (success + failure states).
- Relevant automated tests completed and passing.
- Documentation updated where behavior/contracts changed.
- No false-complete claims; blockers are explicitly documented.

## Suggested Commands
```bash
git fetch origin
git merge origin/main

{{TEST_COMMAND}}
{{SMOKE_COMMAND}}
```

## Checklist
- Branch contains latest `main`.
- Commits are specific, readable, and useful for review history.
- PR title is clear and descriptive.
- PR body includes meaningful implementation and verification details.
- Functionality verified before push/PR.
- Required tests run before push/PR.
