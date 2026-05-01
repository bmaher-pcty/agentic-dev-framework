---
applyTo: "**/*"
---

<!--
  Activate this file only when your project uses a monorepo layout.
  If this is a single-service repository, delete or ignore this file.
  Set {{SERVICE_DIRS}}, {{SERVICE_MANIFEST}}, and {{SHARED_LIB_DIR}} during bootstrap
  or via TOKENS.md when configuring a monorepo project.
-->

# Monorepo Configuration Instructions

This project uses a monorepo layout. These instructions supplement the base framework for multi-service repositories. They apply to all files (`applyTo: "**/*"`) but their rules are only triggered when working across service boundaries.

## Service Boundary Rules

1. Each service in `{{SERVICE_DIRS}}` has its own technology stack. Agents must not apply one service's stack assumptions to another service's code. (Example: do not assume TypeScript linting rules apply in a Python service directory.)
2. When working in `services/[service-name]/`, apply the stack configuration for that service as declared in the Service Manifest below. If the manifest is missing or incomplete, ask the user before proceeding.
3. Cross-service changes — changes to shared libraries, API contracts, or interfaces consumed by more than one service — require Architect sign-off before implementation begins. Write a handoff entry in `docs/open-handoffs.md` if sign-off cannot be obtained in the current session.
4. The Review Council for a cross-service change must include the Guardian perspective reviewing both services' security postures, not only the directly modified service.
5. Never assume a service's internal implementation details are visible to other services. Treat inter-service calls as external API calls: validate inputs and handle failure explicitly.

## Service Manifest

> **Replace the placeholder rows below with your project's actual services.**
> Each row corresponds to one service directory. Keep this table current — it is the first thing agents read when navigating the monorepo.

| Service | Language | Framework | Test Command | Source Dir |
|---------|----------|-----------|--------------|-----------|
{{SERVICE_MANIFEST}}

<!--
Example manifest row format:
| api       | TypeScript | Express  | npm --prefix services/api run test  | services/api/src     |
| worker    | Python     | FastAPI  | pytest services/worker/tests/       | services/worker/src  |
| frontend  | TypeScript | React    | npm --prefix services/web run test  | services/web/src     |
| shared    | TypeScript | N/A      | npm --prefix shared run test        | shared/src           |
-->

## Shared Code Rules

1. Code in `{{SHARED_LIB_DIR}}` is consumed by multiple services. A change to a shared interface is a cross-service change — it requires a cross-service impact review (see Service Boundary Rules #3).
2. Breaking changes to shared interfaces require:
   - An ADR filed in `docs/adr/` before the change is merged.
   - All consuming services updated in the same PR, OR a documented compatibility shim with a removal deadline.
3. Shared utilities must have unit tests in the shared package itself. Consuming services must not test shared library internals — test only the integration contract.
4. Do not add service-specific business logic to `{{SHARED_LIB_DIR}}`. Shared code must be domain-agnostic. If a candidate shared utility contains business rules, keep it in the originating service and expose it via API instead.

## Context Window Management

When working in a monorepo with a large codebase, agents must scope their file reads to the relevant service directory. Loading the entire monorepo into context is not feasible and produces lower-quality results.

Follow this read order:
1. Read this file (the service manifest) to understand the service layout and identify the target service.
2. Read only files in the target service's directory unless the task explicitly involves cross-service concerns.
3. For cross-service concerns, read the shared library interfaces (type definitions, API schemas, contract tests) — not their implementations.
4. Use `docs/project-intelligence.md` as the cross-service pattern reference before reading any implementation files.

## Large Codebase Navigation Protocol

For repositories with more than 50,000 lines of code, agents must follow these steps:

1. **State scope before reading.** Before reading any file, state: which service, which module, and which specific files are targeted. Do not begin reading without an explicit scope statement.
2. **Read contracts before implementations.** Interface files, API schemas, and test files reveal expected behavior faster than implementations. Read them first.
3. **Read test files before implementation files.** Tests describe the intended contract. Read them to understand what the code is supposed to do before reading how it does it.
4. **Use the `#task-triage` skill to scope work.** Large codebases amplify the cost of misdirected work. Classify the task risk level before beginning — a misclassified task in a monorepo can touch dozens of files unexpectedly.
5. **Never read more than 10 files before confirming the approach.** After reading up to 10 files, pause and confirm the implementation approach with the user before continuing. If the approach is clear sooner, confirm earlier.
6. **Surface scope creep immediately.** If completing the task requires reading files outside the initial scope, surface the expanded scope to the user before reading them. Never silently expand the reading scope.
