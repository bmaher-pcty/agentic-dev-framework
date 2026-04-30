---
name: container-operations
description: 'Container Compose patterns for container security, health checks, resource management, and local development reliability.'
---

# Container Operations

## When to Use
- Modifying `{{CONTAINER_COMPOSE_FILE}}` or any Dockerfile.
- Adding new services to the stack.
- Debugging container startup, networking, or health issues.
- Reviewing infrastructure security before a PR merge.

## Procedure
1. Use specific image version  never `:latest` in committed configuration.tags 
2. Define health checks for every service that other services depend on.
3. Set resource limits (memory, CPU) for all services.
4. Run containers as non-root users where possible.
5. Use multi-stage builds to minimize image size and attack surface.
6. Expose only necessary ports; prefer internal container networking.
7. Validate changes with `{{START_COMMAND}}` + health check verification.

## Health Check Pattern
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "{{API_URL}}/health"]
  interval: 10s
  timeout: 5s
  retries: 3
  start_period: 30s
```

## Service Template
```yaml
service-name:
  image: {{BASE_IMAGE}}         #  never :latestPinned 
  restart: unless-stopped
  user: "1000:1000"             # Non-root
  depends_on:
    db:
      condition: service_healthy
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost:PORT/health"]
    interval: 10s
    timeout: 5s
    retries: 3
    start_period: 30s
  environment:
    - NODE_ENV=${NODE_ENV}       # From .env, never hardcoded
```

## Security Checklist
- [ ] No secrets in Dockerfiles or compose files (use environment variables from `.env`).
- [ ] No `:latest` tags in committed configuration.
- [ ] Non-root USER directive in custom Dockerfiles.
- [ ] `.dockerignore` excludes `node_modules`, `.env`, `.git`, `certs/`.
- [ ] Volumes do not expose host system paths unnecessarily.
- [ ] No database ports exposed to host unless required for development.

## Compose Best Practices
- `depends_on` with `condition: service_healthy` for startup ordering.
- Named volumes for persistent data (database).
- Restart policies: `restart: unless-stopped` for services, `restart: "no"` for one-shot tasks.
- Environment variable interpolation from `.env` file (not hardcoded).
- Use profiles for optional services that should not start by default.

## Debugging Commands
```bash
{{CONTAINER_RUNTIME}} compose ps                                  # Service status
{{CONTAINER_RUNTIME}} compose logs -f --tail=50 api               # Stream API logs
{{CONTAINER_RUNTIME}} compose exec api sh                         # Shell into container
{{CONTAINER_RUNTIME}} compose down -v && {{START_COMMAND}}        # Full reset
{{DB_SHELL_COMMAND}}                                              # DB shell
{{CONTAINER_RUNTIME}} stats                                       # Resource usage
```

## Anti-Patterns
- Using `compose up` without health check verification afterward.
- Hardcoding database passwords in compose files instead of `.env`.
- Using `volumes: [".:/app"]` without `.dockerignore` (leaks `.env` into container).
- Missing `start_period` in health checks causing false-negative failures during slow startup.
- Exposing database port to  bypasses application auth.host 
- Using `:latest`  non-deterministic builds that break without warning.tags 
