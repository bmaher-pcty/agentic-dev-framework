# Token Reference — agentic-dev-framework

This document lists all placeholder tokens used throughout the framework files.
It is a reference used during the bootstrap process. Once bootstrap is complete and all tokens are resolved, this file can be deleted from your project.

> **To replace tokens:** See `BOOTSTRAP.prompt.md` for the guided wizard. This file is a read-only reference — do not modify it to replace tokens.

## How Tokens Work

Tokens use the format `{{TOKEN_NAME}}`. They appear in:
- `.github/copilot-instructions.md`
- `.github/agents/*.agent.md`
- `.github/skills/*.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`

When you bootstrap a new project, the smart bootstrap wizard infers most tokens automatically from your language, project type, and stack. You answer 7 questions; the AI fills in the rest and presents a reviewable token map for approval.

## Source Classification

- **Required** — Must be confirmed by a human. Cannot be safely inferred; wrong values break the framework.
- **Inferred from stack** — The bootstrap AI derives these from language, project type, and detected frameworks. Verify if something seems off.
- **Optional (N/A if unused)** — Set to `N/A` for project types that don't use this token (e.g., frontend tokens for CLI projects, container tokens for serverless).

## Token Table

| Token | Category | Source | Purpose | Example Value |
|-------|----------|--------|---------|---------------|
| `{{PROJECT_NAME}}` | Identity | Required | Human-readable name of the project | `My SaaS App` |
| `{{PROJECT_DOMAIN}}` | Identity | Required | The domain/industry area of the project | `engineering management` |
| `{{PRIMARY_LANGUAGE}}` | Identity | Required | Primary programming language | `TypeScript`, `Python`, `Go` |
| `{{SMOKE_COMMAND}}` | Commands | Required | Full smoke test command — the most critical token | `make -f tools/Makefile smoke` |
| `{{SERVER_FRAMEWORK}}` | Stack | Inferred from stack | Backend web framework | `Express.js`, `FastAPI`, `Rails` |
| `{{FRONTEND_FRAMEWORK}}` | Stack | Inferred from stack | Frontend UI framework | `React`, `Vue`, `Svelte` |
| `{{DATABASE}}` | Stack | Inferred from stack | Primary database technology | `PostgreSQL`, `MySQL`, `MongoDB` |
| `{{ORM}}` | Stack | Inferred from stack | ORM or database access library | `Prisma`, `SQLAlchemy`, `ActiveRecord` |
| `{{CONTAINER_RUNTIME}}` | Stack | Inferred from stack | Container runtime | `Docker`, `Podman` |
| `{{CSS_FRAMEWORK}}` | Stack | Inferred from stack | CSS utility framework | `Tailwind`, `UnoCSS` |
| `{{STATE_MANAGEMENT_LIB}}` | Stack | Inferred from stack | Client state management library | `Zustand`, `Pinia`, `Redux` |
| `{{DATA_FETCHING_LIB}}` | Stack | Inferred from stack | Async data fetching library | `React Query`, `SWR`, `Apollo` |
| `{{E2E_TEST_FRAMEWORK}}` | Stack | Inferred from stack | E2E test framework | `Playwright`, `Cypress` |
| `{{UNIT_TEST_FRAMEWORK}}` | Stack | Inferred from stack | Unit/integration test framework | `Jest`, `Vitest`, `pytest` |
| `{{A11Y_TESTING_LIB}}` | Stack | Inferred from stack | Accessibility testing library | `@axe-core/playwright`, `axe-core` |
| `{{VALIDATION_LIBRARY}}` | Stack | Inferred from stack | Input validation library | `Zod`, `Joi`, `pydantic` |
| `{{MIGRATION_TOOL}}` | Stack | Inferred from stack | Database migration tool/command | `npx prisma`, `alembic` |
| `{{TASK_RUNNER}}` | Stack | Inferred from stack | Task runner invocation prefix | `make -f tools/Makefile` |
| `{{TASK_RUNNER_FILE}}` | Stack | Inferred from stack | Task runner file path | `tools/Makefile` |
| `{{REVERSE_PROXY}}` | Stack | Inferred from stack | Reverse proxy server | `nginx`, `caddy` |
| `{{SERVER_SOURCE_DIR}}` | Paths | Inferred from stack | Server/backend source directory | `backend`, `server`, `api` |
| `{{CLIENT_SOURCE_DIR}}` | Paths | Inferred from stack | Client/frontend source directory | `frontend`, `client`, `web` |
| `{{SOURCE_CODE_PATHS}}` | Paths | Inferred from stack | Source file glob patterns | `backend/**/*.ts,frontend/src/**/*.ts` |
| `{{TEST_PATHS}}` | Paths | Inferred from stack | Test file glob patterns | `backend/tests/**/*.ts,frontend/**/*.test.ts` |
| `{{SCRIPTS_DIR}}` | Paths | Inferred from stack | Directory for operational scripts | `tools`, `scripts` |
| `{{FRONTEND_URL}}` | URLs | Inferred from stack | Local frontend URL | `http://localhost:5173` |
| `{{API_URL}}` | URLs | Inferred from stack | Local API URL | `http://localhost:3000` |
| `{{DB_USER}}` | Database | Inferred from stack | Database user | `postgres`, `myapp` |
| `{{DB_NAME}}` | Database | Inferred from stack | Database name | `myapp_dev` |
| `{{CONTAINER_COMPOSE_FILE}}` | Infrastructure | Inferred from stack | Container compose file | `docker-compose.yaml` |
| `{{RUNTIME_VERSION_REQUIREMENT}}` | Infrastructure | Inferred from stack | Runtime version requirement | `Node 22+`, `Python 3.12+` |
| `{{BASE_IMAGE}}` | Infrastructure | Inferred from stack | Container base image | `node:22-alpine`, `python:3.12-slim` |
| `{{DEV_COMMAND}}` | Commands | Inferred from stack | Dev server command | `npm run dev` |
| `{{TEST_COMMAND}}` | Commands | Inferred from stack | General test command | `make -f tools/Makefile test` |
| `{{START_COMMAND}}` | Commands | Inferred from stack | Application start command | `make -f tools/Makefile start` |
| `{{SETUP_COMMAND}}` | Commands | Inferred from stack | Initial setup command | `make -f tools/Makefile setup` |
| `{{UNIT_TEST_COMMAND}}` | Commands | Inferred from stack | Command to run unit tests | `npm --prefix backend run test:ci` |
| `{{E2E_TEST_COMMAND}}` | Commands | Inferred from stack | Command to run E2E tests | `npm --prefix frontend exec playwright test` |
| `{{A11Y_TEST_COMMAND}}` | Commands | Inferred from stack | Command to run a11y tests | `npx playwright test --grep @a11y` |
| `{{DB_SHELL_COMMAND}}` | Commands | Inferred from stack | Command to open DB shell | `docker-compose exec db psql -U postgres` |
| `{{BUNDLE_ANALYSIS_COMMAND}}` | Commands | Optional (N/A if unused) | Bundle analysis command | `npm run build -- --report` |
| `{{DB_EXPLAIN_COMMAND}}` | Commands | Optional (N/A if unused) | DB query explain command | `docker-compose exec db psql -c "EXPLAIN ANALYZE ..."` |
| `{{PERFORMANCE_AUDIT_COMMAND}}` | Commands | Optional (N/A if unused) | Performance audit command | `npx lighthouse http://localhost:3000` |
| `{{ADMIN_TOOL}}` | Infrastructure | Optional (N/A if unused) | Database admin tool | `pgAdmin`, `Adminer` |
| `{{ADMIN_URL}}` | Infrastructure | Optional (N/A if unused) | Admin tool local URL | `localhost:5050` |
| `{{AUTH_PROVIDER}}` | Integrations | Optional (N/A if unused) | Primary authentication provider | `GitHub OAuth`, `Google OAuth` |
| `{{INTEGRATION_PLATFORM}}` | Integrations | Optional (N/A if unused) | Third-party integration platform | `Jira`, `GitHub`, `Slack` |

## Monorepo Tokens

These tokens apply only when the project uses a monorepo layout. Set them when bootstrapping a project of type "Microservices system" or when enabling `.github/instructions/monorepo.instructions.md`.

| Token | Category | Source | Purpose | Example Value |
|-------|----------|--------|---------|---------------|
| `{{SERVICE_DIRS}}` | Monorepo | Optional (N/A if not monorepo) | Comma-separated list of service root directories | `services/api,services/worker,services/frontend` |
| `{{SHARED_LIB_DIR}}` | Monorepo | Optional (N/A if not monorepo) | Shared library directory consumed by multiple services | `shared`, `packages/common`, `libs/shared` |
| `{{SERVICE_MANIFEST}}` | Monorepo | Optional (N/A if not monorepo) | Inline markdown table of services — see `monorepo.instructions.md` for format | *(table rows)* |

## Bootstrap Process

1. Copy this framework into your project's `.github/` directory.
2. Open `BOOTSTRAP.prompt.md` in your AI editor.
3. Answer the 7 smart intake questions — the AI infers the rest from your stack.
4. Review the complete token map and approve or correct it in a single pass.
5. The bootstrap generates tailored files with all tokens replaced.
6. After generation, delete `TOKENS.md` and `BOOTSTRAP.prompt.md` from your project.

## Framework Version
agentic-dev-framework v1.1.0
