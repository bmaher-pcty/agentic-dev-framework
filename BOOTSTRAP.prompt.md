---
name: "Bootstrap Agentic Dev Framework"
description: "Interactive setup prompt to configure the agentic-dev-framework for a specific project by replacing all {{TOKEN}} placeholders with actual values."
argument-hint: "Describe your project briefly or leave blank to be asked questions interactively."
---

# Bootstrap: agentic-dev-framework

This prompt configures the framework for your specific project by replacing all `{{TOKEN}}` placeholders with real values.

## Step 1: Gather Project Information

Answer these questions. The more specific your answers, the better the configuration:

1. **Project name**: What is the human-readable name of this project?
2. **Project domain**: What industry or problem domain does this serve? (e.g., `engineering management`, `e-commerce`, `developer tooling`)
3. **Primary language**: What is the main programming language? (e.g., `TypeScript`, `Python`, `Go`, `Ruby`)
4. **Server framework**: What framework powers the backend/server? (e.g., `Express.js`, `FastAPI`, `Rails`, `Gin`)
5. **Frontend framework**: What UI framework is used, if any? (e.g., `React`, `Vue`, `Svelte`, or `N/A` for non-UI projects)
6. **Database**: What database is used? (e.g., `PostgreSQL`, `MySQL`, `MongoDB`, `SQLite`)
7. **ORM / data access**: How does the code talk to the database? (e.g., `Prisma`, `SQLAlchemy`, `ActiveRecord`, `raw SQL`)
8. **Container runtime**: How are services containerized, if at all? (e.g., `Docker`, `Podman`, or `N/A`)
9. **CSS framework**: What CSS framework or system is used? (e.g., `Tailwind`, `UnoCSS`, `vanilla CSS`, `N/A`)
10. **State management library**: What library manages client state? (e.g., `Zustand`, `Pinia`, `Redux`, `N/A`)
11. **Data fetching library**: What library handles server state / async data? (e.g., `React Query`, `SWR`, `Apollo`, `N/A`)
12. **Unit test framework**: What framework runs unit/integration tests? (e.g., `Jest`, `Vitest`, `pytest`, `RSpec`)
13. **E2E test framework**: What framework runs end-to-end tests? (e.g., `Playwright`, `Cypress`, `N/A`)
14. **Accessibility testing library**: What library runs automated a11y scans? (e.g., `@axe-core/playwright`, `axe-core`, `N/A`)
15. **Validation library**: How is user input validated? (e.g., `Zod`, `Joi`, `pydantic`, `N/A`)
16. **Reverse proxy**: What reverse proxy is used? (e.g., `nginx`, `caddy`, `N/A`)
17. **Task runner**: How are project tasks invoked? (e.g., `make -f tools/Makefile`, `npm run`, `just`)
18. **Task runner file**: Where is the task runner config? (e.g., `tools/Makefile`, `package.json`, `justfile`)
19. **Scripts directory**: Where do operational scripts live? (e.g., `tools`, `scripts`, `bin`)
20. **Server source directory**: Where is server/backend source? (e.g., `backend`, `server`, `api`, `src`)
21. **Client source directory**: Where is client/frontend source? (e.g., `frontend`, `client`, `web`, `N/A`)
22. **Frontend URL**: What URL does the frontend serve locally? (e.g., `http://localhost:5173`)
23. **API URL**: What URL does the API serve locally? (e.g., `http://localhost:3000`)
24. **Database user**: What is the local DB user? (e.g., `postgres`, `root`, `myapp`)
25. **Database name**: What is the local DB name? (e.g., `myapp_dev`, `development`)
26. **Container compose file**: Name of the container compose file (e.g., `docker-compose.yaml`, `compose.yaml`)
27. **Container base image**: Default base image for app containers (e.g., `node:22-alpine`, `python:3.12-slim`)
28. **Migration tool**: Command used to run migrations (e.g., `npx prisma`, `alembic`, `rails db:migrate`)
29. **Admin tool**: Database admin UI, if any (e.g., `pgAdmin`, `Adminer`, `N/A`)
30. **Admin URL**: Local URL for admin tool (e.g., `localhost:5050`, `N/A`)

## Step 2: Generate Token Replacement Map

Based on your answers, produce a complete replacement map in this format:

```
 <your answer>
 <your answer>
 <your answer>
...
```

Include every token from `TOKENS.md`.

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
4. Add `docs/local/` to `.gitignore`.

## Step 7: Verify

Run a quick check:
```bash
grep -r '{{' .github/ --include='*.md' | grep -v 'BOOTSTRAP'
```
If this returns results, some tokens were not replaced. Review and fix before committing.

## Output

After completing all steps, produce a summary:
1. List of all tokens replaced with their values.
2. List of files updated.
3. Any tokens left as-is (with reason, e.g., `N/A` for unused features).
4. Next recommended action (e.g., "Run `{{SMOKE_COMMAND}}` to verify the setup works").
