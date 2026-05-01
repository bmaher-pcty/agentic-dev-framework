---
applyTo: "{{CONTAINER_COMPOSE_FILE}},**/Dockerfile,**/Dockerfile.*,{{REVERSE_PROXY}}/**/*,{{SCRIPTS_DIR}}/**/*"
---

# Container & Infrastructure Instructions

Use these instructions when modifying container configuration, Compose services, reverse proxy config, or operational scripts.

## Service Configuration Rules

1. Every service in `{{CONTAINER_COMPOSE_FILE}}` must have a health check.
2. Use specific image version tags — never `:latest`.
3. Set restart policies: `unless-stopped` for long-running services.
4. Use `depends_on` with `condition: service_healthy` for startup ordering.
5. Resource limits must be set for production-targeted configurations.

## Operational Script Rules

1. All operational scripts live in `{{SCRIPTS_DIR}}/`.
2. Every script must have a corresponding `{{TASK_RUNNER}}` target in `{{TASK_RUNNER_FILE}}`.
3. Scripts must be executable (`chmod +x`) and include a shebang line.
4. Scripts must handle errors with `set -euo pipefail`.
5. Documentation and CI must reference `{{TASK_RUNNER}}` targets, not script paths directly.

## Verification After Changes

1. After modifying `{{CONTAINER_COMPOSE_FILE}}`: run `{{START_COMMAND}}` and verify health endpoints respond.
2. After modifying `{{REVERSE_PROXY}}` config: verify proxy routing with `curl -I http://localhost:PORT/health`.
3. After modifying task runner targets: run the modified target and verify output is correct.

## Security Constraints

1. No secrets in Dockerfiles, compose files, or `{{REVERSE_PROXY}}` configuration.
2. Environment variables for secrets — `.env` is gitignored, `.env.example` is the template.
3. Minimize exposed ports — internal container networking preferred over host port exposure.
4. Non-root USER directive required in custom Dockerfiles for application services.
