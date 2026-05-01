# CI/CD Quality Gate Templates

This directory contains CI/CD pipeline templates that encode the framework's four quality gates across three CI platforms. Select the template matching your platform.

## Platform Templates

| Platform | File | Deploy To |
|----------|------|-----------|
| GitHub Actions | `github-actions.yml` | Copy to `.github/workflows/quality-gates.yml` |
| GitLab CI | `gitlab-ci.yml` | Merge jobs into `.gitlab-ci.yml` |
| Azure DevOps | `azure-devops.yml` | Copy to `azure-pipelines.yml` (or merge jobs) |

Additionally, `handoff-sync.yml` (GitHub Actions) automates creation of GitHub Issues from OPEN handoff entries — see [P6-D2](#handoff-sync-workflow).

---

## The Four Quality Gates

Every template encodes the same four gates:

| Gate | What It Checks | Default Behavior |
|------|---------------|-----------------|
| **Secret Scan** | Hardcoded secrets, tokens, credentials (TruffleHog) | Blocking |
| **Coverage Check** | Test coverage does not fall below threshold (default: 80%) | Blocking |
| **Token Detection** | No unresolved `{{TOKEN}}` placeholders in source or `.github/` | Blocking |
| **Handoff Check** | Fewer than 5 OPEN agent escalations in `docs/open-handoffs.md` | Warning only |

---

## Integrating with an Existing Pipeline

These templates are designed to be **composable**. You do not need to replace your existing CI configuration — add the individual jobs alongside what you already have.

### Incremental adoption order (lowest risk first)

1. **Token Detection** — No external dependencies; catches bootstrap errors immediately. Add first.
2. **Handoff Check** — Pure file read; zero dependencies. Add as a warning gate on day one.
3. **Secret Scan** — Requires Docker or `trufflehog` binary on the runner. Coordinate with your security team before enabling (see SAST Coordination below).
4. **Coverage Check** — Requires your test runner to be configured. Add once your test baseline is stable.

---

## SAST and Framework Security Coordination

If your organization already runs SAST tools (Snyk, Semgrep, Veracode, Checkmarx, SonarQube, GitLab SAST, Azure Defender for DevOps):

**TruffleHog is complementary, not a replacement.**

| Tool Type | What It Finds |
|-----------|--------------|
| SAST (Snyk, Semgrep, Veracode…) | Vulnerability patterns, insecure API usage, dependency CVEs |
| TruffleHog | Hardcoded secrets, tokens, credentials committed to source control |

These address different threat classes. Run both.

**If your SAST tool already scans for hardcoded secrets** (e.g., Semgrep with `secrets` ruleset, GitLab Secret Detection), you may replace the TruffleHog gate with a gate that asserts your SAST run completed and produced zero HIGH/CRITICAL secret findings — rather than running a second scanner.

**Never disable your SAST tool** to adopt this framework. The framework's security rules and your SAST tool are complementary layers. See `.github/instructions/security.instructions.md` — "SAST and Framework Security Coordination" — for the authoritative guidance.

### Pinned Versions Policy

All tool versions in these templates are pinned. This is non-negotiable:

- Pinned versions prevent supply-chain attacks from unreviewed upstream changes.
- Review pinned versions **quarterly** (a comment block at the top of each file tracks the due date).
- Never replace a pinned version with `:latest` or a branch reference.
- When upgrading, check release notes, update the version tag, update the review date, and commit with `chore(ci): update pinned versions (quarterly)`.

---

## Handoff Sync Workflow

`handoff-sync.yml` automates GitHub Issues creation from OPEN handoff entries in `docs/open-handoffs.md`. This is optional — manual ticketing processes are documented in `docs/enterprise/TICKETING-INTEGRATION.md`.

To enable:
1. Copy `handoff-sync.yml` to `.github/workflows/handoff-sync.yml`
2. Ensure the workflow has `issues: write` permission (set in the file)
3. The workflow runs on push to `main` — it will create a GitHub Issue for each new `[OPEN]` handoff entry and add the `agent-handoff` label

---

## Configuring the Coverage Gate

The coverage gate ships with a placeholder command (`{{TEST_COMMAND}}`). Configure it for your stack:

| Stack | Coverage Command |
|-------|-----------------|
| Node.js / Jest | `npm test -- --coverage --coverageThreshold='{"global":{"lines":80}}'` |
| Node.js / Vitest | `npx vitest run --coverage` + vitest config threshold |
| Python / pytest | `pytest --cov=src --cov-fail-under=80` |
| Go | `go test ./... -coverprofile=coverage.out && go tool cover -func=coverage.out` |
| .NET | `dotnet test --collect:"XPlat Code Coverage"` + ReportGenerator |
| Java / Maven | `mvn verify jacoco:check` (configure minimum in pom.xml) |

The minimum coverage floor is **80%**. The aspiration target is **90%+**. Set the gate to your current actual coverage and raise it incrementally — never lower it to make it pass.
