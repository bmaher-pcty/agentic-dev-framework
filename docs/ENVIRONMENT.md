# Environment Setup

> **This file contains environment setup guidance for your bootstrapped project.**
> **Replace all `{{TOKEN}}` placeholders with your project's actual values after running BOOTSTRAP.prompt.md.**

---

## Prerequisites

- `{{RUNTIME_VERSION_REQUIREMENT}}`
- `{{CONTAINER_RUNTIME}}` (if applicable)
- Git

---

## Quick Start

```bash
{{SETUP_COMMAND}}   # Install dependencies and initialize
{{START_COMMAND}}   # Start the development environment
```

---

## Verify the Running Stack

- Frontend: `{{FRONTEND_URL}}`
- API health: `{{API_URL}}/health`

---

## Useful Commands

```bash
# Server / backend
{{DEV_COMMAND}}         # Start dev server
{{TEST_COMMAND}}        # Run tests with coverage

# Client / frontend (if applicable)
{{DEV_COMMAND}}         # Start frontend dev server
{{TEST_COMMAND}}        # Frontend tests with coverage

# Container
{{CONTAINER_RUNTIME}} compose logs -f api    # Stream API logs
{{DB_SHELL_COMMAND}}                          # DB shell

# Database migrations
{{MIGRATION_TOOL}} migrate                    # Run pending migrations

# Smoke verification (full end-to-end check)
{{SMOKE_COMMAND}}
```

---

## Environment Variables

See `.env.example` for all required variables.
Copy `.env.example` to `.env` and fill in values before starting.

For operator-specific setup (OAuth registration, API keys, tenant IDs), see `docs/local/` (gitignored).

---

## Notes

- Never commit `.env` or `.env.local` to version control.
- Use `{{TASK_RUNNER}} <target>` instead of invoking scripts directly.
- All scripts live in `{{SCRIPTS_DIR}}/`.
