---
name: "Bootstrap Agentic Dev Framework"
description: "Smart setup wizard: answer 7 questions, the AI infers the rest. Produces a complete, ready-to-use .github/ configuration in minutes."
argument-hint: "Describe your project or answer the 7 setup questions interactively."
---

# Bootstrap: agentic-dev-framework

> Run this in GitHub Copilot Chat or any capable AI coding assistant with file-system access.
> Have your project open — the AI will scan it to pre-fill many answers automatically.

---

## Phase 1: Smart Intake (7 Questions)

Ask the developer these seven questions. Present them as a numbered list. Do not ask more than these seven unless a follow-up is needed to resolve genuine ambiguity. Let the answers unlock the rest.

**Question 1: Project identity**
"What is your project's name, and in one sentence, what does it do?"
_(Used in file headers, agent examples, and README.)_

**Question 2: Primary language and project type**
"What is your primary programming language, and which type best describes your project?"
Choose from: `Web application (full-stack)` / `API or backend service` / `CLI tool` / `Data pipeline` / `Mobile app` / `Library or SDK` / `Microservices system` / `Other`
_(This single answer unlocks ~70% of all token inferences.)_

**Question 3: Core technology decisions already made**
"List any frameworks, databases, or tools you've already committed to. Leave blank if none yet."
_(Free-form. E.g., "React frontend, FastAPI backend, PostgreSQL, Docker". The AI will parse this and lock in those tokens.)_

**Question 4: Has this project been started?**
"Has any code been written yet, or is this a fresh project?"
If yes: "Great — I'll scan your project's `package.json`, `pyproject.toml`, `go.mod`, or equivalent to auto-detect the rest of the stack."
_(Scanning can pre-fill 90% of tokens without asking. The AI should attempt to read these files from the current working directory.)_

**Question 5: Team size**
"How many people are actively developing this? (solo / small 2-5 / medium 6-15 / large 15+)"
_(Used to scale agent coordination language — solo projects get simplified handoffs.)_

**Solo Developer Note:** If your team size is `solo`, the bootstrap will apply the Solo Profile: 5 default agents (Engineer, System Architect, Review Council, QA, Innovator) with a 4-perspective Micro Council as the default review depth. All other agents are available via explicit invocation but deactivated by default. All quality guarantees (completion gate, security non-negotiable, no false-complete) apply identically in the Solo Profile.

**Question 6: Any agents to deactivate?**
"Are there any of the 11 agents you definitely don't need? Common exclusions:
- No UI → deactivate Bold UX Designer, Accessibility
- No containers → deactivate DevOps/Infrastructure
- No concern with creativity/research → deactivate Innovator"
_(Optional. If blank, all 11 are active.)_

**Question 7: What's your smoke test?**
"What single command verifies your whole application is working end-to-end? (or say 'not set up yet')"
_(This is the most critical token. If unknown, the AI will suggest a placeholder based on the stack.)_

---

## Phase 2: AI Inference Pass

After receiving answers to all seven questions, perform the following inference pass **before asking any more questions**. This is the core of the smart bootstrap.

### Inference Rules (apply in order)

**From Language + Project Type, infer with HIGH confidence (no follow-up needed):**

| If language is... | And project type is... | Then infer... |
|---|---|---|
| TypeScript/JavaScript | Web (full-stack) | `{{RUNTIME}}` = Node.js; `{{UNIT_TEST_FRAMEWORK}}` = Vitest (suggest) or Jest; `{{SOURCE_CODE_PATHS}}` = `src/**/*.ts,src/**/*.tsx`; `{{TEST_PATHS}}` = `**/*.test.ts,**/*.spec.ts` |
| TypeScript/JavaScript | API/backend | `{{RUNTIME}}` = Node.js; `{{UNIT_TEST_FRAMEWORK}}` = Vitest or Jest; `{{SOURCE_CODE_PATHS}}` = `src/**/*.ts`; `{{TEST_PATHS}}` = `**/*.test.ts` |
| Python | Any | `{{RUNTIME}}` = Python; `{{UNIT_TEST_FRAMEWORK}}` = pytest; `{{SOURCE_CODE_PATHS}}` = `**/*.py`; `{{TEST_PATHS}}` = `tests/**/*.py,**/*_test.py` |
| Go | Any | `{{RUNTIME}}` = Go; `{{UNIT_TEST_FRAMEWORK}}` = go test; `{{SOURCE_CODE_PATHS}}` = `**/*.go`; `{{TEST_PATHS}}` = `**/*_test.go` |
| Java/Kotlin | Any | `{{RUNTIME}}` = JVM; `{{UNIT_TEST_FRAMEWORK}}` = JUnit; `{{SOURCE_CODE_PATHS}}` = `src/main/**/*.java`; `{{TEST_PATHS}}` = `src/test/**/*.java` |
| Ruby | Any | `{{RUNTIME}}` = Ruby; `{{UNIT_TEST_FRAMEWORK}}` = RSpec; `{{SOURCE_CODE_PATHS}}` = `app/**/*.rb,lib/**/*.rb`; `{{TEST_PATHS}}` = `spec/**/*.rb` |
| Rust | Any | `{{RUNTIME}}` = Rust; `{{UNIT_TEST_FRAMEWORK}}` = cargo test; `{{SOURCE_CODE_PATHS}}` = `src/**/*.rs`; `{{TEST_PATHS}}` = `src/**/*.rs` (inline tests) |

**From Stack choices (Question 3 or scanned from project files), infer with HIGH confidence:**

| If stack includes... | Infer... |
|---|---|
| React | `{{FRONTEND_FRAMEWORK}}` = React; `{{CLIENT_SOURCE_DIR}}` = `frontend/src` (or `src` if monorepo) |
| Vue | `{{FRONTEND_FRAMEWORK}}` = Vue; `{{CSS_FRAMEWORK}}` = (ask or suggest Tailwind) |
| Svelte | `{{FRONTEND_FRAMEWORK}}` = Svelte |
| Next.js | `{{FRONTEND_FRAMEWORK}}` = Next.js; `{{SERVER_FRAMEWORK}}` = Next.js API routes |
| Express | `{{SERVER_FRAMEWORK}}` = Express; `{{SERVER_SOURCE_DIR}}` = `src` or `backend/src` |
| FastAPI | `{{SERVER_FRAMEWORK}}` = FastAPI; `{{VALIDATION_LIBRARY}}` = Pydantic |
| Django | `{{SERVER_FRAMEWORK}}` = Django; `{{ORM}}` = Django ORM; `{{MIGRATION_TOOL}}` = Django migrations |
| Rails | `{{SERVER_FRAMEWORK}}` = Rails; `{{ORM}}` = ActiveRecord; `{{MIGRATION_TOOL}}` = Rails migrations |
| Gin | `{{SERVER_FRAMEWORK}}` = Gin |
| Prisma | `{{ORM}}` = Prisma; `{{MIGRATION_TOOL}}` = Prisma Migrate; `{{SCHEMA_FILE}}` = `prisma/schema.prisma` |
| SQLAlchemy + Alembic | `{{ORM}}` = SQLAlchemy; `{{MIGRATION_TOOL}}` = Alembic |
| PostgreSQL | `{{DATABASE}}` = PostgreSQL; `{{DB_USER}}` = postgres (suggest); `{{DB_NAME}}` = `<project_name>_dev` |
| MongoDB | `{{DATABASE}}` = MongoDB; `{{ORM}}` = (suggest Mongoose or native driver) |
| Docker | `{{CONTAINER_RUNTIME}}` = Docker; `{{CONTAINER_COMPOSE_FILE}}` = `docker-compose.yaml` |
| Tailwind | `{{CSS_FRAMEWORK}}` = Tailwind CSS |
| Playwright | `{{E2E_TEST_FRAMEWORK}}` = Playwright; `{{A11Y_TESTING_LIB}}` = @axe-core/playwright (suggest) |
| Cypress | `{{E2E_TEST_FRAMEWORK}}` = Cypress |
| Zod | `{{VALIDATION_LIBRARY}}` = Zod |
| Pydantic | `{{VALIDATION_LIBRARY}}` = Pydantic |
| npm/package.json detected | `{{TASK_RUNNER}}` = npm run; `{{TASK_RUNNER_FILE}}` = package.json |
| Makefile detected | `{{TASK_RUNNER}}` = make; `{{TASK_RUNNER_FILE}}` = Makefile (or `tools/Makefile` if under tools/) |
| No frontend | `{{FRONTEND_FRAMEWORK}}` = N/A; `{{CSS_FRAMEWORK}}` = N/A; `{{STATE_MANAGEMENT_LIB}}` = N/A; `{{DATA_FETCHING_LIB}}` = N/A; `{{CLIENT_SOURCE_DIR}}` = N/A; `{{FRONTEND_URL}}` = N/A |
| CLI tool | All frontend tokens = N/A; no UX Designer or Accessibility agents |
| No containers | `{{CONTAINER_RUNTIME}}` = N/A; `{{CONTAINER_COMPOSE_FILE}}` = N/A; `{{REVERSE_PROXY}}` = N/A |

**Command inference (MEDIUM confidence — present to user for confirmation):**

Infer commands from task runner + stack:
- If `npm run` detected: `{{DEV_COMMAND}}` = `npm run dev`, `{{TEST_COMMAND}}` = `npm test`, `{{START_COMMAND}}` = `npm start`, `{{SETUP_COMMAND}}` = `npm install`
- If `make` detected: derive from Makefile targets if readable, otherwise suggest `make dev`, `make test`, `make start`
- If Python/pytest: `{{TEST_COMMAND}}` = `pytest`, `{{DEV_COMMAND}}` = `uvicorn main:app --reload` (if FastAPI)
- If Go: `{{TEST_COMMAND}}` = `go test ./...`, `{{DEV_COMMAND}}` = `go run .`

**If team size is `solo`:**
- Apply Solo Framework Profile: activate Engineer, System Architect, Review Council, QA, Innovator
- Deactivate by default: PM (activate when product has users), UX Designer (activate when UI work begins), Technical Writer (activate when docs become a priority), Accessibility, DevOps/Infrastructure, Performance
- Default council depth: Micro Council (Advocate + Guardian + Craftsperson + User Champion)
- Full 7-perspective council available via: "Run full council review on [PR/change]"
- Note to developer: "I've applied the Solo Profile. You can activate any deactivated agent by invoking them directly. All quality guarantees remain enforced — the profile reduces coordination overhead, not quality bars."

**URL inference (MEDIUM confidence — present for confirmation):**
- TypeScript full-stack with Vite detected: `{{FRONTEND_URL}}` = `http://localhost:5173`, `{{API_URL}}` = `http://localhost:3000`
- FastAPI: `{{API_URL}}` = `http://localhost:8000`
- Rails: `{{API_URL}}` = `http://localhost:3000`
- Default if unknown: `{{FRONTEND_URL}}` = `http://localhost:3000`, `{{API_URL}}` = `http://localhost:8080`

**Remaining tokens default:**
- `{{BUNDLE_SIZE_THRESHOLD}}` = 50KB (reasonable default)
- `{{BUNDLE_ANALYSIS_COMMAND}}` = N/A (unless build tool detected)
- `{{DB_EXPLAIN_COMMAND}}` = `EXPLAIN ANALYZE` (generic SQL; adjust for DB)
- `{{PERFORMANCE_AUDIT_COMMAND}}` = N/A (set up manually if needed)
- `{{INTEGRATION_PLATFORM}}` = N/A (unless mentioned)
- `{{SESSION_SECRET}}` = SESSION_SECRET (conventional env var name)

---

## Phase 3: Token Resolution Review

Based on your answers and stack inference, here is your proposed token resolution. **Review each group and flag any corrections before I generate files.**

---

### Group 1 — Identity (Required: confirm these)
*These are unique to your project. I cannot infer them.*

| Token | Resolved Value | Source | Status |
|-------|---------------|--------|--------|
| `{{PROJECT_NAME}}` | [your answer] | You provided | ✅ |
| `{{PROJECT_DOMAIN}}` | [your answer] | You provided | ✅ |
| `{{PRIMARY_LANGUAGE}}` | [your answer] | You provided | ✅ |

---

### Group 2 — Stack (Inferred: spot-check for accuracy)
*Inferred from your language + framework choices. Verify these are correct.*

| Token | Resolved Value | Source | Status |
|-------|---------------|--------|--------|
| `{{SERVER_FRAMEWORK}}` | [inferred] | Inferred from language | ✅/❓ |
| `{{DATABASE}}` | [your answer] | You provided | ✅ |
| `{{ORM}}` | [inferred or provided] | ... | ✅/❓ |
| `{{FRONTEND_FRAMEWORK}}` | [your answer or N/A] | You provided | ✅ |
| `{{CSS_FRAMEWORK}}` | [inferred or N/A] | ... | ✅/❓ |
| ... | | | |

---

### Group 3 — Commands (Inferred: most likely to need correction)
*Inferred from your stack and task runner. These are where errors most commonly occur — verify carefully.*

| Token | Resolved Value | Source | Status |
|-------|---------------|--------|--------|
| `{{SMOKE_COMMAND}}` | [inferred or your answer] | ... | ✅/❓ |
| `{{TEST_COMMAND}}` | [inferred] | ... | ✅/❓ |
| `{{START_COMMAND}}` | [inferred] | ... | ✅/❓ |
| `{{SETUP_COMMAND}}` | [inferred] | ... | ✅/❓ |
| `{{DEV_COMMAND}}` | [inferred] | ... | ✅/❓ |
| ... | | | |

---

### Group 4 — Paths and URLs (Inferred: verify directory structure)
*Inferred from your project structure answers. Confirm these match your actual directories.*

| Token | Resolved Value | Source | Status |
|-------|---------------|--------|--------|
| `{{SERVER_SOURCE_DIR}}` | [your answer] | You provided | ✅ |
| `{{CLIENT_SOURCE_DIR}}` | [your answer or N/A] | You provided | ✅ |
| `{{SOURCE_CODE_PATHS}}` | [inferred glob] | Inferred from language | ✅/❓ |
| `{{FRONTEND_URL}}` | [inferred] | ... | ✅/❓ |
| `{{API_URL}}` | [inferred] | ... | ✅/❓ |
| ... | | | |

---

### Group 5 — Optional / N/A (Pre-set: no review needed unless your project uses these)
*These are set to N/A based on your answers. If your project does use any of these, flag them now.*

| Token | Resolved Value | Reason |
|-------|---------------|--------|
| `{{E2E_TEST_FRAMEWORK}}` | N/A | You indicated no E2E tests |
| `{{A11Y_TESTING_LIB}}` | N/A | You indicated no frontend |
| ... | | |

---

**Any corrections?** Reply with the Group number and token to change, or type "All approved" to proceed to file generation.

---

## Phase 4: Targeted Follow-Ups (Only If Needed)

If the developer corrects tokens in Phase 3, update the map, show only the changed rows, and ask "Anything else to change, or shall I proceed?"

If the smoke command is still unknown (developer said "not set up yet" in Question 7), ask: "What directory structure does your project use? I'll suggest a smoke command based on your task runner."

---

## Phase 5: Generate Files

With the approved map, generate the tailored `.github/` folder:

1. Copy every file from the framework.
2. Apply all token replacements from the approved map.
3. For any token still unknown (marked ❓), leave it as `{{TOKEN}}` and add it to the manual review list.
4. For agents deactivated in Question 6:
   - Create a stub file with frontmatter only and: `> This agent was deactivated at bootstrap. Restore content from agentic-dev-framework to re-activate.`
5. For skills that are N/A for this project type (e.g., `ui-component-design.md` for a CLI project):
   - Add `status: inactive` to frontmatter and a note at the top.
6. Resolve `applyTo` globs in `testing.instructions.md`, `security.instructions.md`, and `council-review.instructions.md` using `{{SOURCE_CODE_PATHS}}` and `{{TEST_PATHS}}` from the approved map.
7. Generate `docs/FRAMEWORK_SETUP.md` with the following content (uses `docs/templates/FRAMEWORK_SETUP.md.template.md` as the structural template — follow that template's section order and table formats):
   - **Bootstrap date** — the date bootstrap was run.
   - **Framework version** — v1.0.0 (or read from README.md if available).
   - **Active agents** — list of N of 11 active agents.
   - **Deactivated agents** — list with the reason each was deactivated.
   - **Token resolution summary** — table of token → resolved value (or ❓ if not resolved), grouped by category (Identity, Stack, Commands, Paths, Optional/N/A).
   - **Tokens requiring manual review** — list of any ❓ tokens still needing resolution.

   > **Important:** `docs/FRAMEWORK_SETUP.md` must not contain secrets, credential values, or environment-specific paths. It documents which agents are active and which tokens were resolved — not the resolved values of secrets. Tokens whose values are secrets (e.g., `{{SESSION_SECRET}}`, API keys, database passwords) must appear as `[secret — set in .env, not tracked]` rather than their actual values.

   `docs/FRAMEWORK_SETUP.md` is tracked in the project (not gitignored). It helps future team members understand the framework configuration without re-running bootstrap.

---

## Phase 6: Summary and Next Steps

Produce this summary after generation:

```
## Bootstrap Complete

### Token Resolution
[Table of all tokens and their final values]

### Confidence Summary
- ✅ Confirmed: [N] tokens
- 🟡 Inferred: [N] tokens (verify if behavior seems off)
- ❓ Manual review needed: [N] tokens (listed below)

### Manual Review Required
[List any ❓ tokens with a one-line note on how to find the value]

### Active Agents ([N] of 11)
[list]

### Deactivated Agents
[list with reason]

### Next Steps
1. Copy `.github/` and `docs/FRAMEWORK_SETUP.md` into your project root
2. Commit: `git add .github/ docs/FRAMEWORK_SETUP.md && git commit -m "chore: add agentic dev framework"`
3. Verify smoke command: run `[SMOKE_COMMAND]` — if it fails, fix the command first
4. First agent invocation: `@engineer: [describe your first task]`
5. Before first PR: run `@review-council` for a full perspective review
6. Innovator tip: `@innovator: [describe any design decision you're unsure about]` to get a creative second opinion before committing
```

---

## Rules (Non-Negotiable)

- Do NOT ask more than 7 questions in Phase 1 unless a follow-up is strictly required to resolve genuine ambiguity.
- Do NOT present the full token map question-by-question — always as a single table for batch review.
- Do NOT leave `{{TOKEN}}` in the output without calling it out in the manual review list.
- Do NOT soften any security constraint. Security rules are non-negotiable regardless of project type.
- Do NOT remove the completion gate logic from any agent or instruction file.
- Do NOT remove the "invisible success is a defect" rule from any file where it appears.
- ALWAYS attempt to scan the project's dependency manifest (package.json, pyproject.toml, go.mod, Cargo.toml, Gemfile) before asking about stack — many tokens can be resolved without a single question.
