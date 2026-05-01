# Token Reference — agentic-dev-framework

This document lists all placeholder tokens used throughout the framework files.
Run `BOOTSTRAP.prompt.md` to replace these tokens with your project's actual values.

## How Tokens Work

Tokens use the format `{{TOKEN_NAME}}`. They appear in:
- `.github/copilot-instructions.md`
- `.github/agents/*.agent.md`
- `.github/skills/*.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`

When you bootstrap a new project, the bootstrap prompt walks you through replacing each token.

## Token Table

| Token | Purpose | Example Value |
|-------|---------|---------------|
| `{{PROJECT_NAME}}` | Human-readable name of the project | `My SaaS App` |
| `{{PROJECT_DOMAIN}}` | The domain/industry area of the project | `engineering management` |
| `{{SERVER_FRAMEWORK}}` | Backend web framework | `Express.js`, `FastAPI`, `Rails` |
| `{{FRONTEND_FRAMEWORK}}` | Frontend UI framework | `React`, `Vue`, `Svelte` |
| `{{DATABASE}}` | Primary database technology | `PostgreSQL`, `MySQL`, `MongoDB` |
| `{{ORM}}` | ORM or database access library | `Prisma`, `SQLAlchemy`, `ActiveRecord` |
| `{{CONTAINER_RUNTIME}}` | Container runtime | `Docker`, `Podman` |
| `{{PRIMARY_LANGUAGE}}` | Primary programming language | `TypeScript`, `Python`, `Go` |
| `{{SERVER_SOURCE_DIR}}` | Server/backend source directory | `backend`, `server`, `api` |
| `{{CLIENT_SOURCE_DIR}}` | Client/frontend source directory | `frontend`, `client`, `web` |
| `{{E2E_TEST_FRAMEWORK}}` | E2E test framework | `Playwright`, `Cypress` |
| `{{UNIT_TEST_FRAMEWORK}}` | Unit/integration test framework | `Jest`, `Vitest`, `pytest` |
| `{{CSS_FRAMEWORK}}` | CSS utility framework | `Tailwind`, `UnoCSS` |
| `{{STATE_MANAGEMENT_LIB}}` | Client state management library | `Zustand`, `Pinia`, `Redux` |
| `{{DATA_FETCHING_LIB}}` | Async data fetching library | `React Query`, `SWR`, `Apollo` |
| `{{A11Y_TESTING_LIB}}` | Accessibility testing library | `@axe-core/playwright`, `axe-core` |
| `{{A11Y_TEST_COMMAND}}` | Command to run a11y tests | `npx playwright test --grep @a11y` |
| `{{AUTH_PROVIDER}}` | Primary authentication provider | `GitHub OAuth`, `Google OAuth` |
| `{{INTEGRATION_PLATFORM}}` | Third-party integration platform | `Jira`, `GitHub`, `Slack` |
| `{{FRONTEND_URL}}` | Local frontend URL | `http://localhost:5173` |
| `{{API_URL}}` | Local API URL | `http://localhost:3000` |
| `{{DB_USER}}` | Database user | `postgres`, `myapp` |
| `{{DB_NAME}}` | Database name | `myapp_dev` |
| `{{TASK_RUNNER}}` | Task runner invocation prefix | `make -f tools/Makefile` |
| `{{TASK_RUNNER_FILE}}` | Task runner file path | `tools/Makefile` |
| `{{SCRIPTS_DIR}}` | Directory for operational scripts | `tools`, `scripts` |
| `{{REVERSE_PROXY}}` | Reverse proxy server | `nginx`, `caddy` |
| `{{RUNTIME_VERSION_REQUIREMENT}}` | Runtime version requirement | `Node 22+`, `Python 3.12+` |
| `{{CONTAINER_COMPOSE_FILE}}` | Container compose file | `docker-compose.yaml` |
| `{{ADMIN_TOOL}}` | Database admin tool | `pgAdmin`, `Adminer` |
| `{{ADMIN_URL}}` | Admin tool local URL | `localhost:5050` |
| `{{MIGRATION_TOOL}}` | Database migration tool/command | `npx prisma`, `alembic` |
| `{{VALIDATION_LIBRARY}}` | Input validation library | `Zod`, `Joi`, `pydantic` |
| `{{DB_SHELL_COMMAND}}` | Command to open DB shell | `docker-compose exec db psql -U postgres` |
| `{{UNIT_TEST_COMMAND}}` | Command to run unit tests | `npm --prefix backend run test:ci` |
| `{{E2E_TEST_COMMAND}}` | Command to run E2E tests | `npm --prefix frontend exec playwright test` |
| `{{BASE_IMAGE}}` | Container base image | `node:22-alpine`, `python:3.12-slim` |
| `{{SMOKE_COMMAND}}` | Full smoke test command | `make -f tools/Makefile smoke` |
| `{{TEST_COMMAND}}` | General test command | `make -f tools/Makefile test` |
| `{{START_COMMAND}}` | Application start command | `make -f tools/Makefile start` |
| `{{SETUP_COMMAND}}` | Initial setup command | `make -f tools/Makefile setup` |
| `{{DEV_COMMAND}}` | Dev server command | `npm run dev` |
| `{{SOURCE_CODE_PATHS}}` | Source file glob patterns | `backend/**/*.ts,frontend/src/**/*.ts` |
| `{{TEST_PATHS}}` | Test file glob patterns | `backend/tests/**/*.ts,frontend/**/*.test.ts` |
| `{{BUNDLE_ANALYSIS_COMMAND}}` | Bundle analysis command | `npm run build -- --report` |
| `{{DB_EXPLAIN_COMMAND}}` | DB query explain command | `docker-compose exec db psql -c "EXPLAIN ANALYZE ..."` |
| `{{PERFORMANCE_AUDIT_COMMAND}}` | Performance audit command | `npx lighthouse http://localhost:3000` |

## Bootstrap Process

1. Copy this framework into your project's `.github/` directory.
2. Open `BOOTSTRAP.prompt.md` in your AI editor.
3. Answer the questions to configure your project's token values.
4. The bootstrap prompt will produce a replacement mapping to apply across all files.
5. After replacement, delete `TOKENS.md` and `BOOTSTRAP.prompt.md` from your project.

## Framework Version
agentic-dev-framework v1.0.0
