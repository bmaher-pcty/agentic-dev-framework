---
applyTo: "{{SERVER_SOURCE_DIR}}/**/*.ts,{{SERVER_SOURCE_DIR}}/**/*.js,{{CLIENT_SOURCE_DIR}}/**/*.ts,{{CLIENT_SOURCE_DIR}}/**/*.tsx,{{CLIENT_SOURCE_DIR}}/**/*.js,.github/workflows/**/*.yml,.github/workflows/**/*.yaml,{{CONTAINER_COMPOSE_FILE}},{{REVERSE_PROXY}}/**/*,**/Dockerfile,**/Dockerfile.*,certs/**/*"
---

# Security Implementation Instructions

Use these instructions for authentication, authorization, OAuth integration, token handling, and secret-safe operations.

## Baseline Requirements

1. Validate all request input before business logic execution.
2. Enforce authentication on protected API routes.
3. Enforce per-user ownership checks for persisted and fetched records.
4. Return 404 for non-owned or non-existent private resources when enumeration risk exists.
5. Keep session and auth data in HTTP-only secure cookie and token-safe transport paths.

## OAuth And Token Rules

1. Use OAuth 2.0 code flow with PKCE for provider integrations.
2. Validate provider scopes at setup and fail closed when required scopes are missing.
3. Rotate refresh tokens safely and record refresh failures without leaking credential material.
4. Use short-lived state values for OAuth callbacks and enforce expiration.
5. Apply rate limiting to OAuth callback and token-refresh endpoints to prevent code/state replay abuse.
6. Apply CSRF protection to cookie-authenticated state-changing routes.

## Secret Management Rules

1. Never hardcode secrets, tokens, API keys, or client secrets.
2. Keep secrets in environment variables only.
3. Keep `.env` and `.env.local` out of version control.
4. Keep `.env.example` as placeholders only.

## Logging And Error Rules

1. Never log raw credentials, tokens, authorization headers, or password fields.
2. Redact sensitive values in structured logs and error paths.
3. Keep production errors actionable without exposing internals.

## Required Env Variables

> Replace this list with your project's actual env variables after running BOOTSTRAP.prompt.md.
> Add a placeholder to `.env.example` for every new secret introduced.

Common variables for auth/OAuth features:
- `JWT_SECRET` — signs access tokens.
- `DATABASE_URL` — database connection string.
- `FRONTEND_URL` — used for CORS and OAuth callback.
- `SESSION_EXPIRES_IN` — token lifetime.

When introducing new auth providers or secrets, add a placeholder to `.env.example` in the same change.

## SAST and Framework Security Coordination

This framework's security rules operate **alongside** your SAST tooling — they address different layers and must never be traded against each other.

### What SAST Does

Static Analysis Security Testing tools (Snyk, Semgrep, Veracode, Checkmarx, SonarQube, GitLab SAST, CodeQL) find:
- Known vulnerability patterns in code (SQL injection, XSS, insecure deserialization, etc.)
- Dependency CVEs and outdated packages with known exploits
- Misconfigured security settings in infrastructure-as-code
- Some categories of hardcoded secrets (coverage varies by tool and ruleset)

### What This Framework Does

The framework's security rules enforce:
- Architectural decisions that prevent entire classes of vulnerabilities (OAuth PKCE, HTTP-only cookies, input validation at every layer)
- Code review gates that catch insecure patterns before SAST runs (Guardian perspective in council reviews)
- Operational hygiene that SAST cannot verify (secrets in environment variables only, no credentials in logs, health check endpoint design)
- Agent behavior constraints that prevent AI-assisted code from bypassing security controls

### The Coordination Rule

**SAST must never be disabled to adopt this framework.**

These are additive controls. If your CI pipeline includes a SAST tool, keep it running. Add the framework's CI quality gates alongside it — do not replace one with the other. Specifically:

1. The secret-scan gate (TruffleHog) in `docs/templates/ci/` is complementary to SAST secret detection, not a replacement. If your SAST tool already covers hardcoded secret detection with HIGH/CRITICAL findings, you may substitute the TruffleHog gate with a gate that asserts your SAST run completed with zero HIGH/CRITICAL secret findings — but you must keep one of the two gates.
2. SAST findings at HIGH or CRITICAL severity are treated as Guardian-level findings in council reviews — they cannot be downgraded in severity by other council perspectives.
3. If a SAST tool is intentionally disabled or excluded from a path (e.g., a legacy file excluded by configuration), that exclusion must be documented in `docs/adr/` with rationale and a compensating control.
4. When AI-generated code is introduced, run SAST on the diff before merging — AI assistants can produce code that passes human review but fails static analysis. See `docs/enterprise/SBOM.md` for the Guardian finding format for AI-introduced dependencies.

### When SAST and Framework Rules Conflict

They should not conflict — but if a SAST tool flags code that this framework's rules permit, or vice versa:
- The stricter control wins. Never relax either to satisfy the other.
- If the conflict reflects a genuine ambiguity, create an ADR documenting the decision.
- Surface the conflict to the Guardian perspective in the next council review.

## Client-Side Security Rules

1. Never store access tokens, session tokens, or sensitive credentials in `localStorage` or `sessionStorage` — use HTTP-only secure cookies or in-memory state only.
2. Sanitize all user-supplied content before inserting into the DOM. Never use `innerHTML`, `dangerouslySetInnerHTML`, or `eval()` with user-supplied strings without explicit sanitization.
3. Apply Content Security Policy headers that restrict inline script execution. Reference the CSP header requirements in the Infrastructure Security Rules section.
4. Validate that third-party scripts are loaded from known, pinned CDN URLs and that Subresource Integrity (SRI) hashes are included where supported.
5. Never log authentication tokens, session identifiers, or sensitive form field values in the browser console.
6. All client-side validation is defense-in-depth only — every input must also be validated server-side. Client-only validation is not a security control.
7. OAuth tokens received by the frontend must be forwarded to the backend immediately and never persisted in client storage.

## Infrastructure Security Rules

1. {{CONTAINER_RUNTIME}} images must use specific version tags, not `:latest`.
2. Containers must run as non-root users for application services.
3. {{REVERSE_PROXY}} configuration must include security headers: `X-Frame-Options`, `X-Content-Type-Options`, `Strict-Transport-Security`, `X-XSS-Protection`, `Content-Security-Policy`, `Referrer-Policy`.
4. TLS certificates must not be committed to source control — `certs/` must be gitignored.
5. {{CONTAINER_RUNTIME}} Compose must not expose database ports to the host unless explicitly required for development.
6. Health check endpoints must not expose internal system details beyond status and timestamp.
