---
name: "Bootstrap Agentic Dev Framework"
description: "Smart setup wizard: answer 7 questions, the AI infers the rest. Produces a ready-to-use .github/ configuration in under 10 minutes."
argument-hint: "Describe your project or answer the 7 setup questions interactively."
---

# Bootstrap: agentic-dev-framework

> Run this in GitHub Copilot Chat or any capable AI coding assistant with file-system access.
> Have your project open — the AI will scan manifest files to pre-fill most answers.

---

## Phase 1: Smart Intake (7 Questions)

Ask these seven questions as a numbered list. Do not ask more unless a follow-up is strictly needed to resolve genuine ambiguity. Attempt to scan `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or `Gemfile` before asking about stack — scanning pre-fills most answers without questions.

**Q1: Project identity**
"What is your project's name, and in one sentence, what does it do?"

**Q2: Primary language and project type**
"What is your primary programming language, and which type best describes your project?"
Choices: `Web app (full-stack)` / `API or backend service` / `CLI tool` / `Data pipeline` / `Library or SDK` / `Other`

**Q3: Core technology decisions already made**
"List any frameworks, databases, or tools you've already committed to. Leave blank if none yet."
(E.g., "React, FastAPI, PostgreSQL, Docker")

**Q4: Has any code been written yet?**
"Has any code been written yet, or is this a fresh project?"
If yes: scan the dependency manifest to auto-detect remaining stack tokens.

**Q5: Team size**
"How many people are actively developing this? (solo / 2–10 / 10+)"

**Q6: Any of the 5 agents to skip?** *(Optional)*
"Any of these you definitely don't need: Engineer, System Architect, Review Council, Veteran QA, Innovator? Leave blank to keep all."

**Q7: Smoke test command**
"What single command verifies your whole application works end-to-end? (or say 'not set up yet')"
This is the most critical token. If unknown, suggest a placeholder based on the stack.

---

## Phase 2: AI Inference Pass

After the 7 questions, run this inference pass **before asking anything else**. This is the core of the smart bootstrap.

### From Language + Project Type (HIGH confidence — no follow-up needed)

| Language | Project Type | Infer |
|----------|-------------|-------|
| TypeScript/JavaScript | Web (full-stack) | `{{RUNTIME}}` = Node.js; `{{UNIT_TEST_FRAMEWORK}}` = Vitest (suggest) or Jest; `{{SOURCE_CODE_PATHS}}` = `src/**/*.ts,src/**/*.tsx`; `{{TEST_PATHS}}` = `**/*.test.ts,**/*.spec.ts` |
| TypeScript/JavaScript | API/backend | `{{RUNTIME}}` = Node.js; `{{UNIT_TEST_FRAMEWORK}}` = Vitest or Jest; `{{SOURCE_CODE_PATHS}}` = `src/**/*.ts`; `{{TEST_PATHS}}` = `**/*.test.ts` |
| Python | Any | `{{RUNTIME}}` = Python; `{{UNIT_TEST_FRAMEWORK}}` = pytest; `{{SOURCE_CODE_PATHS}}` = `**/*.py`; `{{TEST_PATHS}}` = `tests/**/*.py,**/*_test.py` |
| Go | Any | `{{RUNTIME}}` = Go; `{{UNIT_TEST_FRAMEWORK}}` = go test; `{{SOURCE_CODE_PATHS}}` = `**/*.go`; `{{TEST_PATHS}}` = `**/*_test.go` |
| Java/Kotlin | Any | `{{RUNTIME}}` = JVM; `{{UNIT_TEST_FRAMEWORK}}` = JUnit; `{{SOURCE_CODE_PATHS}}` = `src/main/**/*.java`; `{{TEST_PATHS}}` = `src/test/**/*.java` |
| Ruby | Any | `{{RUNTIME}}` = Ruby; `{{UNIT_TEST_FRAMEWORK}}` = RSpec; `{{SOURCE_CODE_PATHS}}` = `app/**/*.rb,lib/**/*.rb`; `{{TEST_PATHS}}` = `spec/**/*.rb` |

### From Stack (HIGH confidence — no follow-up needed)

| If stack includes… | Infer… |
|--------------------|--------|
| React | `{{FRONTEND_FRAMEWORK}}` = React; `{{CLIENT_SOURCE_DIR}}` = `frontend/src` or `src` |
| Vue | `{{FRONTEND_FRAMEWORK}}` = Vue |
| Next.js | `{{FRONTEND_FRAMEWORK}}` = Next.js; `{{SERVER_FRAMEWORK}}` = Next.js API routes |
| Express | `{{SERVER_FRAMEWORK}}` = Express; `{{SERVER_SOURCE_DIR}}` = `src` or `backend/src` |
| FastAPI | `{{SERVER_FRAMEWORK}}` = FastAPI; `{{VALIDATION_LIBRARY}}` = Pydantic |
| Django | `{{SERVER_FRAMEWORK}}` = Django; `{{ORM}}` = Django ORM; `{{MIGRATION_TOOL}}` = Django migrations |
| Rails | `{{SERVER_FRAMEWORK}}` = Rails; `{{ORM}}` = ActiveRecord; `{{MIGRATION_TOOL}}` = Rails migrations |
| Prisma | `{{ORM}}` = Prisma; `{{MIGRATION_TOOL}}` = Prisma Migrate |
| PostgreSQL | `{{DATABASE}}` = PostgreSQL |
| MongoDB | `{{DATABASE}}` = MongoDB |
| Docker | `{{CONTAINER_RUNTIME}}` = Docker; `{{CONTAINER_COMPOSE_FILE}}` = `docker-compose.yaml` |
| Tailwind | `{{CSS_FRAMEWORK}}` = Tailwind CSS |
| Playwright | `{{E2E_TEST_FRAMEWORK}}` = Playwright |
| npm detected | `{{TASK_RUNNER}}` = npm run; `{{TASK_RUNNER_FILE}}` = package.json |
| Makefile detected | `{{TASK_RUNNER}}` = make; `{{TASK_RUNNER_FILE}}` = Makefile |
| No frontend | All frontend tokens = N/A |
| No containers | All container tokens = N/A |

### Commands (MEDIUM confidence — present for confirmation)

- npm: `{{DEV_COMMAND}}` = `npm run dev`, `{{TEST_COMMAND}}` = `npm test`, `{{SMOKE_COMMAND}}` = `npm test`
- Python/pytest: `{{TEST_COMMAND}}` = `pytest`, `{{DEV_COMMAND}}` = `uvicorn main:app --reload` (FastAPI)
- Go: `{{TEST_COMMAND}}` = `go test ./...`, `{{DEV_COMMAND}}` = `go run .`
- URL defaults: TypeScript/Vite → `{{FRONTEND_URL}}` = `http://localhost:5173`; FastAPI → `{{API_URL}}` = `http://localhost:8000`

---

## Phase 3: Token Resolution Review

Show all resolved tokens as a single table grouped by category (Identity, Stack, Commands, Paths/URLs, N/A). Mark each as ✅ confirmed, 🟡 inferred, or ❓ needs review.

Ask once: "Any corrections? Reply with the token name and new value, or type 'All approved'."

---

## Phase 4: Targeted Follow-Ups (Only If Needed)

If tokens were corrected in Phase 3, update the map, show only changed rows, and ask "Anything else, or shall I proceed?"

If smoke command is still unknown, ask: "What task runner does your project use? I'll suggest a smoke command."

---

## Phase 5: Generate Files

With the approved token map:

1. Copy every file from the framework's `.github/` folder.
2. Apply all token replacements from the approved map.
3. For any token still ❓, leave it as `{{TOKEN}}` and add it to the manual review list.
4. For agents deactivated in Q6: create a stub file with frontmatter only and a note: `> This agent was deactivated at bootstrap. Restore content from agentic-dev-framework to re-activate.`
5. Resolve `applyTo` globs in `testing.instructions.md` and `security.instructions.md` using `{{SOURCE_CODE_PATHS}}` and `{{TEST_PATHS}}`.
6. Copy root `AGENTS.md` with tokens resolved (the drop-in single-file starter).

---

## Phase 6: Summary

```
## Bootstrap Complete

### Token Resolution
[Table of all tokens → resolved values]

### Confidence Summary
- ✅ Confirmed: N tokens
- 🟡 Inferred: N tokens (verify if behavior seems off)
- ❓ Manual review needed: N tokens

### Active Agents
[list]

### Next Steps
1. Copy `.github/` and `AGENTS.md` into your project root
2. git add .github/ AGENTS.md && git commit -m "chore: add agentic dev framework"
3. Verify: run [SMOKE_COMMAND]
4. First use: @engineer: [describe your first task]
5. Before first PR: @review-council on your changes
```

---

## Phase 7: Bootstrap Verification

Run these three checks and report results in the Bootstrap Complete summary.

### Check 1: Token Scan
`grep -r '{{[A-Z_]*}}' .github/ --include="*.md"`
Expected: zero matches. If tokens remain, list each with file and line in "Manual Review Required."

### Check 2: Agent Count
`ls .github/agents/*.agent.md | wc -l`
Expected: matches Active Agents count (active + stubs = total).

### Check 3: Smoke Command Resolved
Confirm `{{SMOKE_COMMAND}}` does not contain literal `{{`. If it does: flag as `REQUIRES-HUMAN-VERIFICATION` with note: "Set smoke command manually in `.github/instructions/testing.instructions.md`."

---

## Rules (Non-Negotiable)

- Do NOT ask more than 7 questions in Phase 1 unless strictly required.
- Do NOT present tokens question-by-question — always as a single table.
- Do NOT leave `{{TOKEN}}` in output without listing it in manual review.
- Do NOT soften security constraints. They are non-negotiable regardless of project type.
- Do NOT remove the completion gate logic or the verification label definitions.
- ALWAYS scan the dependency manifest before asking about stack.
