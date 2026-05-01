---
name: "Codebase Audit"
description: "Full codebase audit using the Review Council methodology across code quality, security, and UI/UX."
argument-hint: "Optionally specify focus areas (e.g., 'security-heavy', 'UX-focused') or leave blank for balanced audit."
---

Perform a comprehensive codebase audit using the Review Council methodology.

## Scope

Review the project codebase across three domains:

### Domain 1: Code Quality
- Type safety (no unsafe type escapes, proper generics, discriminated unions).
- Error handling consistency and structured error responses.
- Test coverage quality and gap identification.
- DRY compliance and pattern consistency across modules.
- Service layer architecture and module boundaries.
- API design coherence and validation completeness.

### Domain 2: Security
- Authentication and authorization enforcement on all routes.
- OAuth implementation correctness (PKCE, state, scopes, token lifecycle).
- Secret management (no hardcoded values, no log exposure, env safety).
- Input validation on all request boundaries.
- Infrastructure security (containers, reverse proxy, TLS, CORS, headers).
- Error responses that do not leak internals.

### Domain 3: UI/UX
- Visual hierarchy and information architecture.
- Navigation clarity, discoverability, and routing correctness.
- Accessibility (WCAG AA: contrast, keyboard, screen reader, ARIA).
- Result visibility after primary user actions.
- Error state handling, loading states, and empty states.
- Responsive design and component consistency.
- CSS framework usage patterns and design system coherence.

## Procedure

1. Read key server-side files: routes, middleware, services, schema, tests.
2. Read key client-side files: pages, components, API layer, state management, tests.
3. Read configuration: compose file, reverse proxy config, env examples.
4. Run all seven council perspectives across each domain. The Innovator runs last, after all other perspectives have spoken, and presents a reframing or cross-domain insight not raised by any other perspective.
5. Synthesize cross-domain findings.
6. Produce a prioritized improvement plan.

## Output

1. **Executive Summary** — Overall codebase health across the three domains.
2. **Advocate Report** — Top 10 strengths (with file references) that define the quality baseline.
3. **Code Quality Findings** — Craftsperson + Skeptic findings with severity and file references.
4. **Security Findings** — Guardian findings with severity, attack vector, and remediation.
5. **UI/UX Findings** — User Champion findings with severity, affected page/component, and fix.
6. **Synthesizer Recommendations** — Cross-cutting patterns to consolidate and spread.
7. **Prioritized Improvement Backlog** — Numbered, severity-tagged, with owner perspective and effort estimate (S/M/L).

8. **Innovator Reframe** — The Innovator's alternative framing or cross-domain insight not raised by any other perspective, with a proposed spike if meaningful uncertainty remains.

9. **Project Intelligence Update** — Format per `council-review.instructions.md` requirements: new anti-patterns discovered, architectural decisions to lock, coverage hot spots, open Innovator experiments. If nothing new, write: "No updates — intelligence file current."

## Rules

- Be exhaustive but actionable. Every finding must have a specific fix.
- Advocate speaks first per domain.
- Security findings from the Guardian cannot be deprioritized.
- The Innovator perspective must run last, after all other perspectives. Reference `.github/skills/council-review.md` for Innovator validity rules.
- Reference `.github/skills/council-review.md` for the full methodology.
- Apply the path-scoped security and testing instructions in `.github/instructions/` when evaluating findings.
