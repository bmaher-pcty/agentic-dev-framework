---
name: "Bootstrap Agentic Dev Framework"
description: "Interactive setup prompt to configure the agentic-dev-framework for a specific project by replacing all {{TOKEN}} placeholders with actual values."
argument-hint: "Describe your project briefly or leave blank to be asked questions interactively."
---

# Bootstrap: agentic-dev-framework

This prompt configures the framework for your specific project by replacing all `{{TOKEN}}` placeholders with real values. It is token-complete: every row in `TOKENS.md` has a corresponding question below.

## Step 1A: Project Identity and Stack

1. **Project name** (`PROJECT_NAME`): Human-readable name of this project.
2. **Project domain** (`PROJECT_DOMAIN`): Industry / problem domain (e.g., `engineering management`, `e-commerce`, `developer tooling`).
3. **Primary language** (`PRIMARY_LANGUAGE`): Main programming language (e.g., `TypeScript`, `Python`, `Go`, `Ruby`).
4. **Server framework** (`SERVER_FRAMEWORK`): Backend framework (e.g., `Express.js`, `FastAPI`, `Rails`, `Gin`).
5. **Frontend framework** (`FRONTEND_FRAMEWORK`): UI framework (e.g., `React`, `Vue`, `Svelte`, or `N/A`).
6. **Database** (`DATABASE`): Primary DB (e.g., `PostgreSQL`, `MySQL`, `MongoDB`, `SQLite`).
7. **ORM / data access** (`ORM`): How code talks to the DB (e.g., `Prisma`, `SQLAlchemy`, `ActiveRecord`, `raw SQL`).
8. **Container runtime** (`CONTAINER_RUNTIME`): How services run (e.g., `Docker`, `Podman`, `N/A`).
9. **CSS framework** (`CSS_FRAMEWORK`): CSS system (e.g., `Tailwind`, `UnoCSS`, `vanilla CSS`, `N/A`).
10. **State management library** (`STATE_MANAGEMENT_LIB`): Client state lib (e.g., `Zustand`, `Pinia`, `Redux`, `N/A`).
11. **Data fetching library** (`DATA_FETCHING_LIB`): Server-state lib (e.g., `React Query`, `SWR`, `Apollo`, `N/A`).
12. **Unit test framework** (`UNIT_TEST_FRAMEWORK`): Unit/integration test runner (e.g., `Jest`, `Vitest`, `pytest`, `RSpec`).
13. **E2E test framework** (`E2E_TEST_FRAMEWORK`): E2E test runner (e.g., `Playwright`, `Cypress`, `N/A`).
14. **Accessibility testing library** (`A11Y_TESTING_LIB`): a11y scanner (e.g., `@axe-core/playwright`, `axe-core`, `N/A`).
15. **Validation library** (`VALIDATION_LIBRARY`): Input validation (e.g., `Zod`, `Joi`, `pydantic`, `N/A`).
16. **Reverse proxy** (`REVERSE_PROXY`): Reverse proxy (e.g., `nginx`, `caddy`, `N/A`).
17. **Task runner** (`TASK_RUNNER`): Task runner invocation prefix (e.g., `make -f tools/Makefile`, `npm run`, `just`).
18. **Task runner file** (`TASK_RUNNER_FILE`): Task runner config file (e.g., `tools/Makefile`, `package.json`, `justfile`).
19. **Scripts directory** (`SCRIPTS_DIR`): Where operational scripts live (e.g., `tools`, `scripts`, `bin`).
20. **Server source directory** (`SERVER_SOURCE_DIR`): Server/backend source (e.g., `backend`, `server`, `api`, `src`).
21. **Client source directory** (`CLIENT_SOURCE_DIR`): Client/frontend source (e.g., `frontend`, `client`, `web`, `N/A`).
22. **Frontend URL** (`FRONTEND_URL`): Local frontend URL (e.g., `http://localhost:5173`).
23. **API URL** (`API_URL`): Local API URL (e.g., `http://localhost:3000`).
24. **Database user** (`DB_USER`): Local DB user (e.g., `postgres`, `root`, `myapp`).
25. **Database name** (`DB_NAME`): Local DB name (e.g., `myapp_dev`, `development`).
26. **Container compose file** (`CONTAINER_COMPOSE_FILE`): Compose file name (e.g., `docker-compose.yaml`, `compose.yaml`).
27. **Migration tool** (`MIGRATION_TOOL`): Migration command (e.g., `npx prisma`, `alembic`, `rails db:migrate`).
28. **Admin tool** (`ADMIN_TOOL`): DB admin UI, if any (e.g., `pgAdmin`, `Adminer`, `N/A`).
29. **Admin URL** (`ADMIN_URL`): Local URL for admin tool (e.g., `localhost:5050`, `N/A`).

## Step 1B: Path Globs

30. **Source code paths** (`SOURCE_CODE_PATHS`): Comma-separated globs covering all production source files. Example: `backend/**/*.ts,frontend/src/**/*.ts,frontend/src/**/*.tsx`.
31. **Test paths** (`TEST_PATHS`): Comma-separated globs covering all test files. Example: `backend/tests/**/*.ts,frontend/**/*.test.ts,frontend/integration_tests/**/*.ts`.

## Step 1C: Verification Commands

32. **Smoke command** (`SMOKE_COMMAND`): Full smoke workflow (e.g., `make -f tools/Makefile smoke`).
33. **Test command** (`TEST_COMMAND`): General test command (e.g., `make -f tools/Makefile test`).
34. **Start command** (`START_COMMAND`): Application start command (e.g., `make -f tools/Makefile start`).
35. **Setup command** (`SETUP_COMMAND`): Initial setup command (e.g., `make -f tools/Makefile setup`).
36. **Dev command** (`DEV_COMMAND`): Dev server command (e.g., `npm run dev`).
37. **Unit test command** (`UNIT_TEST_COMMAND`): Unit/integration tests (e.g., `npm --prefix backend run test:ci`).
38. **E2E test command** (`E2E_TEST_COMMAND`): E2E tests (e.g., `npm --prefix frontend exec playwright test`).
39. **A11y test command** (`A11Y_TEST_COMMAND`): Accessibility tests (e.g., `npx playwright test --grep @a11y`).
40. **Bundle analysis command** (`BUNDLE_ANALYSIS_COMMAND`): Bundle analyzer (e.g., `npm run build -- --report`).
41. **DB explain command** (`DB_EXPLAIN_COMMAND`): DB query explain (e.g., `docker-compose exec db psql -c "EXPLAIN ANALYZE ..."`).
42. **Performance audit command** (`PERFORMANCE_AUDIT_COMMAND`): Perf audit (e.g., `npx lighthouse http://localhost:3000`).
43. **DB shell command** (`DB_SHELL_COMMAND`): Open DB shell (e.g., `docker-compose exec db psql -U postgres`).

## Step 1D: Runtime / Image

44. **Runtime version requirement** (`RUNTIME_VERSION_REQUIREMENT`): Required runtime version (e.g., `Node 22+`, `Python 3.12+`).
45. **Container base image** (`BASE_IMAGE`): Default app container base image (e.g., `node:22-alpine`, `python:3.12-slim`).

## Step 1E: Integrations

46. **Auth provider** (`AUTH_PROVIDER`): Primary authentication provider (e.g., `GitHub OAuth`, `Google OAuth`, `Auth0`, `N/A`).
47. **Integration platform** (`INTEGRATION_PLATFORM`): Primary third-party integration platform (e.g., `Jira`, `GitHub`, `Slack`, `N/A`).

## Step 2: Generate Token Replacement Map

Based on your answers, produce a complete replacement map. Every row in `TOKENS.md` must appear exactly once. Use this template (replace each `<value>` with the answer collected above):

```
{{PROJECT_NAME}}                  → <Project name>
{{PROJECT_DOMAIN}}                → <Project domain>
{{PRIMARY_LANGUAGE}}              → <Primary language>
{{SERVER_FRAMEWORK}}              → <Server framework>
{{FRONTEND_FRAMEWORK}}            → <Frontend framework>
{{DATABASE}}                      → <Database>
{{ORM}}                           → <ORM>
{{CONTAINER_RUNTIME}}             → <Container runtime>
{{CSS_FRAMEWORK}}                 → <CSS framework>
{{STATE_MANAGEMENT_LIB}}          → <State management library>
{{DATA_FETCHING_LIB}}             → <Data fetching library>
{{UNIT_TEST_FRAMEWORK}}           → <Unit test framework>
{{E2E_TEST_FRAMEWORK}}            → <E2E test framework>
{{A11Y_TESTING_LIB}}              → <Accessibility testing library>
{{VALIDATION_LIBRARY}}            → <Validation library>
{{REVERSE_PROXY}}                 → <Reverse proxy>
{{TASK_RUNNER}}                   → <Task runner>
{{TASK_RUNNER_FILE}}              → <Task runner file>
{{SCRIPTS_DIR}}                   → <Scripts directory>
{{SERVER_SOURCE_DIR}}             → <Server source dir>
{{CLIENT_SOURCE_DIR}}             → <Client source dir>
{{FRONTEND_URL}}                  → <Frontend URL>
{{API_URL}}                       → <API URL>
{{DB_USER}}                       → <DB user>
{{DB_NAME}}                       → <DB name>
{{CONTAINER_COMPOSE_FILE}}        → <Compose file>
{{MIGRATION_TOOL}}                → <Migration tool>
{{ADMIN_TOOL}}                    → <Admin tool>
{{ADMIN_URL}}                     → <Admin URL>
{{SOURCE_CODE_PATHS}}             → <Source code globs>
{{TEST_PATHS}}                    → <Test globs>
{{SMOKE_COMMAND}}                 → <Smoke command>
{{TEST_COMMAND}}                  → <Test command>
{{START_COMMAND}}                 → <Start command>
{{SETUP_COMMAND}}                 → <Setup command>
{{DEV_COMMAND}}                   → <Dev command>
{{UNIT_TEST_COMMAND}}             → <Unit test command>
{{E2E_TEST_COMMAND}}              → <E2E test command>
{{A11Y_TEST_COMMAND}}             → <A11y test command>
{{BUNDLE_ANALYSIS_COMMAND}}       → <Bundle analysis command>
{{DB_EXPLAIN_COMMAND}}            → <DB explain command>
{{PERFORMANCE_AUDIT_COMMAND}}     → <Performance audit command>
{{DB_SHELL_COMMAND}}              → <DB shell command>
{{RUNTIME_VERSION_REQUIREMENT}}   → <Runtime version requirement>
{{BASE_IMAGE}}                    → <Base image>
{{AUTH_PROVIDER}}                 → <Auth provider>
{{INTEGRATION_PLATFORM}}          → <Integration platform>
```

Before continuing, assert: every row in `TOKENS.md` has a value above. If any token in `TOKENS.md` does not appear in the map, stop and re-prompt.

## Step 3: Apply Replacements

For each file in `.github/` that contains `{{TOKEN}}` placeholders:
1. Read the file.
2. Apply all replacements from the map.
3. Write the updated file.
4. Confirm each file is updated.

Key files to update:
- `.github/copilot-instructions.md`
- `.github/agents/*.agent.md`
- `.github/skills/*.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`

## Step 4: Customize Phase Structure

In `.github/copilot-instructions.md`, replace the "Development Phase Structure" section with your project's actual roadmap phases. If you don't have phases yet, leave the defaults and update them after your first planning session.

## Step 5: Set Scope-to-Test Mapping

In `.github/instructions/testing.instructions.md`, update the Scope-to-Test Mapping table to reflect your actual directory structure and test commands.

## Step 6: Cleanup

After all tokens are replaced:
1. Delete `BOOTSTRAP.prompt.md` from your project repository.
2. Delete `TOKENS.md` from your project repository.
3. Commit the configured `.github/` directory.
4. If your project's `.gitignore` does not already exclude `docs/local/`, add it now.

## Step 7: Verify

Run a quick check:
```bash
grep -rE '\{\{[A-Z_]+\}\}' .github/ docs/ README.md | grep -v 'TOKENS.md\|BOOTSTRAP.prompt.md'
```
If this returns results, some tokens were not replaced. Review and fix before committing.

## Output

After completing all steps, produce a summary:
1. List of all tokens replaced with their values.
2. List of files updated.
3. Any tokens left as-is (with reason, e.g., `N/A` for unused features).
4. Next recommended action (e.g., "Run `{{SMOKE_COMMAND}}` to verify the setup works").
