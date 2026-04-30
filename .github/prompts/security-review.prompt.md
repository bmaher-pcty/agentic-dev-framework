---
name: "Security Review"
description: "Perform a targeted security review for auth, OAuth, secrets, logging, and access-control changes."
argument-hint: "Provide the changed files or feature area to review."
---

Perform a focused security review for the supplied changes.

Checklist:
1. Input validation on all request boundaries.
2. Authentication and ownership enforcement on protected routes.
3. OAuth flow safety: PKCE, state validation, scope checks, token handling.
4. Secret safety: no hardcoded credentials, no leaks in logs, `.env.example` placeholders updated when needed.
5. Error and status behavior does not expose sensitive internals or resource enumeration details.

Output format:
1. Findings ordered by severity.
2. Affected files and reason.
3. Recommended fix for each finding.
4. Residual risk after fixes.
