# Project Roadmap Template

> Copy this file to `docs/ROADMAP.md` and replace the phase descriptions with your project's actual phases.
> Reference this in `copilot-instructions.md` under "Development Phase Structure".

## Phase 1: Scaffolding
**Goal:** Establish project foundation — directory structure, CI/CD, core dependencies, local dev environment.
**Done when:** New team members can clone, run `{{SETUP_COMMAND}}`, and have a working local environment. CI passes on an empty commit.

## Phase 2: Core Domain
**Goal:** Implement the primary domain entities, their storage, and the core business logic.
**Done when:** Core entities are persisted, validated, and tested. API endpoints exist for primary CRUD operations. No external integrations required to use the core flow.

## Phase 3: Integration
**Goal:** Connect to external services — auth providers, third-party APIs, webhooks, messaging systems.
**Done when:** At least one external integration is fully operational with OAuth/API key flow, error handling, retry logic, and observability.

## Phase 4: Observability
**Goal:** Add structured logging, health checks, metrics, alerting, and request tracing.
**Done when:** A production incident can be diagnosed from logs alone. Health endpoints exist. Alerts fire for P1 failure conditions.

## Phase 5: Hardening
**Goal:** Performance optimization, security audit, accessibility review, load testing, documentation completion.
**Done when:** Load test passes at target RPS. Security review clean. WCAG 2.1 AA audit passed. Runbook complete.

---

*Add, remove, or rename phases to match your project's actual structure. The names above are suggestions, not requirements.*
