# TaskFlow — Bootstrap Answers

> This file shows exactly what answers to give `BOOTSTRAP.prompt.md` to produce the files in this example directory.
> **Do not copy these files into your project directly.** Run `BOOTSTRAP.prompt.md` with your own answers instead.

---

## Project Identity

1. **Project name:** TaskFlow
2. **What it does:** A task and project management API with a React frontend — teams create projects, assign tasks, track progress, and view analytics dashboards.

## Technology Stack — Backend

3. **Primary language:** Python
4. **Backend framework:** FastAPI
5. **Database:** PostgreSQL
6. **ORM:** SQLAlchemy
7. **Migration tool:** Alembic

## Technology Stack — Frontend

8. **Has frontend:** Yes
9. **Frontend framework:** React + TypeScript
10. **CSS framework:** Tailwind CSS
11. **State management:** Zustand
12. **Data fetching:** TanStack Query (React Query v5)

## Infrastructure

13. **Containers:** Yes — Docker
14. **Reverse proxy:** Nginx
15. **Task runner:** Make (tools/Makefile)
16. **Scripts directory:** tools/

## Testing

17. **Unit/integration test framework:** pytest
18. **E2E test framework:** Playwright
19. **Accessibility testing:** @axe-core/playwright
20. **Smoke command:** `make smoke`
21. **Test command:** `make test`

## Auth and Integration

22. **OAuth provider:** GitHub OAuth
23. **Input validation:** Pydantic (built into FastAPI)

## Project Structure

24. **Backend source:** `backend/src`
25. **Frontend source:** `frontend/src`
26. **Source glob:** `backend/**/*.py,frontend/src/**/*.ts,frontend/src/**/*.tsx`
27. **Test glob:** `backend/tests/**/*.py,frontend/src/**/*.test.ts,frontend/src/**/*.spec.ts`
28. **Schema file:** `backend/alembic/` (migrations directory)

## Team and Scale

29. **Team size:** Small (3 developers)
30. **Profile:** Small Team — all agents active, full 7-perspective council

---

## Token Resolution Map

| Token | Resolved Value |
|-------|---------------|
| `{{PROJECT_NAME}}` | TaskFlow |
| `{{PROJECT_DOMAIN}}` | task and project management |
| `{{PRIMARY_LANGUAGE}}` | Python |
| `{{SERVER_FRAMEWORK}}` | FastAPI |
| `{{FRONTEND_FRAMEWORK}}` | React |
| `{{DATABASE}}` | PostgreSQL |
| `{{ORM}}` | SQLAlchemy |
| `{{MIGRATION_TOOL}}` | Alembic |
| `{{CONTAINER_RUNTIME}}` | Docker |
| `{{CONTAINER_COMPOSE_FILE}}` | docker-compose.yaml |
| `{{REVERSE_PROXY}}` | Nginx |
| `{{TASK_RUNNER}}` | make -f tools/Makefile |
| `{{TASK_RUNNER_FILE}}` | tools/Makefile |
| `{{SCRIPTS_DIR}}` | tools |
| `{{SMOKE_COMMAND}}` | make smoke |
| `{{TEST_COMMAND}}` | make test |
| `{{START_COMMAND}}` | make start |
| `{{SETUP_COMMAND}}` | make setup |
| `{{DEV_COMMAND}}` | make dev |
| `{{UNIT_TEST_FRAMEWORK}}` | pytest |
| `{{E2E_TEST_FRAMEWORK}}` | Playwright |
| `{{A11Y_TESTING_LIB}}` | @axe-core/playwright |
| `{{A11Y_TEST_COMMAND}}` | npx playwright test --grep @a11y |
| `{{VALIDATION_LIBRARY}}` | Pydantic |
| `{{CSS_FRAMEWORK}}` | Tailwind CSS |
| `{{AUTH_PROVIDER}}` | GitHub |
| `{{RUNTIME}}` | Python |
| `{{RUNTIME_VERSION_REQUIREMENT}}` | 3.11+ |
| `{{SERVER_SOURCE_DIR}}` | backend/src |
| `{{CLIENT_SOURCE_DIR}}` | frontend/src |
| `{{SOURCE_CODE_PATHS}}` | backend/**/*.py,frontend/src/**/*.ts,frontend/src/**/*.tsx |
| `{{TEST_PATHS}}` | backend/tests/**/*.py,frontend/src/**/*.test.ts |
| `{{FRONTEND_URL}}` | http://localhost:5173 |
| `{{API_URL}}` | http://localhost:8000 |
| `{{DB_USER}}` | postgres |
| `{{DB_NAME}}` | taskflow_dev |
| `{{DB_SHELL_COMMAND}}` | docker compose exec db psql -U postgres taskflow_dev |
| `{{SMOKE_COMMAND}}` | make smoke |
