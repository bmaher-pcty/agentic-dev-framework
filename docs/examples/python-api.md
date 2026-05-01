# Example: Python / FastAPI Backend Service

> **What this shows:** Bootstrapping the framework for a Python API service. The framework catches an N+1 query that slipped past code review — because neither reviewer was looking at the ORM-generated SQL.

---

## Project Context

**Project:** Metrics API — a backend service that aggregates team performance metrics  
**Stack:** Python 3.11, FastAPI, SQLAlchemy + Alembic, PostgreSQL, Docker, pytest, no frontend

---

## Step 1: Bootstrap Session

**Question 1:** "Metrics API — aggregates engineering team metrics (cycle time, PR throughput, deploy frequency) from GitHub and Jira for reporting dashboards."

**Question 2:** "Python. API or backend service."

**Question 3:** "FastAPI, SQLAlchemy, Alembic, PostgreSQL, Docker, pytest. No frontend — pure API."

**Question 4:** "Yes — we have a `pyproject.toml`." *(AI scans it and confirms FastAPI, SQLAlchemy, Alembic, pytest, pydantic.)*

**Question 5:** "Small team (3 devs). Small Team profile."

**Question 6:** "Deactivate: Bold UX Designer, Accessibility. No UI."

**Question 7:** "`pytest tests/smoke/` — we have a smoke test suite that hits the running API."

**Inference pass result (partial):**

| Token | Resolved Value | Source |
|-------|---------------|--------|
| `{{PROJECT_NAME}}` | Metrics API | Provided |
| `{{PRIMARY_LANGUAGE}}` | Python | Provided |
| `{{SERVER_FRAMEWORK}}` | FastAPI | Provided |
| `{{DATABASE}}` | PostgreSQL | Provided |
| `{{ORM}}` | SQLAlchemy | Provided |
| `{{MIGRATION_TOOL}}` | Alembic | Provided |
| `{{VALIDATION_LIBRARY}}` | Pydantic | Inferred from FastAPI |
| `{{UNIT_TEST_FRAMEWORK}}` | pytest | Provided |
| `{{SMOKE_COMMAND}}` | `pytest tests/smoke/` | Provided |
| `{{TEST_COMMAND}}` | `pytest` | Inferred from pytest |
| `{{CONTAINER_RUNTIME}}` | Docker | Provided |
| `{{FRONTEND_FRAMEWORK}}` | N/A | No frontend |
| `{{CSS_FRAMEWORK}}` | N/A | No frontend |
| `{{SOURCE_CODE_PATHS}}` | `**/*.py` | Inferred from Python |
| `{{TEST_PATHS}}` | `tests/**/*.py,**/*_test.py` | Inferred from Python |
| `{{API_URL}}` | `http://localhost:8000` | Inferred from FastAPI default |

**Clarification pass (Phase 2):**

> "Since your project has no frontend, I've deactivated Bold UX Designer and Accessibility agents (plus their associated skills: `ui-component-design`, `ui-principles`, `e2e-testing-patterns`, `async-data-fetching`, `client-state-management`, `accessibility-testing`). These are stubbed, not deleted. Confirm or adjust?"

Developer replies: "Confirmed."

**Bootstrap Verification (Phase 7):**
```
### Bootstrap Verification
- ✅ Token scan: 0 unresolved tokens
- ✅ Agent count: 10 agent files (10 active, 2 stubs: bold-ux-designer, accessibility)
- ✅ Smoke command resolved: pytest tests/smoke/
- ✅ FRAMEWORK_SETUP.md generated: no tokens remaining

Overall: PASS
```

---

## Step 2: The N+1 Query — Caught After Merge (But Before Production)

Four weeks in. The team shipped a `/teams/{team_id}/metrics` endpoint that returns metrics for all repositories owned by a team.

The endpoint had passed code review. Both reviewers checked the logic, the validation, and the test coverage. The tests passed.

**The code:**
```python
@router.get("/teams/{team_id}/metrics")
async def get_team_metrics(
    team_id: int,
    db: Session = Depends(get_db),
    current_user: User = Depends(require_auth)
):
    team = db.query(Team).filter(
        Team.id == team_id,
        Team.owner_id == current_user.id  # ownership check ✓
    ).first()
    
    if not team:
        raise HTTPException(status_code=404)
    
    repos = db.query(Repository).filter(
        Repository.team_id == team_id
    ).all()
    
    # For each repo, fetch its recent metrics
    result = []
    for repo in repos:
        metrics = db.query(Metric).filter(
            Metric.repo_id == repo.id,
            Metric.recorded_at >= thirty_days_ago()
        ).order_by(Metric.recorded_at.desc()).all()
        result.append({"repo": repo.name, "metrics": metrics})
    
    return result
```

**What the QA agent found during regression:**

```
@qa: Run a regression pass on the /teams endpoints before we promote to staging.
```

QA flagged the endpoint as "untested for performance at realistic data volumes" — the test suite used teams with 2–3 repositories. The production dataset had teams with 40–80 repositories.

**Performance agent invocation:**

```
@performance: The /teams/{team_id}/metrics endpoint is in the regression list.
Review it for query efficiency before staging promotion.
```

**Performance agent finding:**

> The loop on line 24 (`for repo in repos`) executes one database query per repository. For a team with 60 repositories, this is 61 queries (1 for the team, 1 for repositories, 60 for metrics). At 5ms per query, that's 300ms of database time before serialization.
>
> SQLAlchemy can resolve this with an eager-loaded join. The metrics can be fetched in a single query using a subquery or `selectinload`.

**The fix:**
```python
from sqlalchemy.orm import selectinload

repos = db.query(Repository).options(
    selectinload(Repository.recent_metrics)
).filter(
    Repository.team_id == team_id
).all()
```

With the addition of a `recent_metrics` relationship with a date filter in the model, this reduces 61 queries to 2.

**What was caught:** The N+1 query was invisible in code review because SQLAlchemy's lazy loading makes it look like a simple attribute access. The framework's QA regression pass flagged it as undertested at realistic scale; the Performance agent identified the exact fix.

---

## Step 3: Security Finding — Missing Rate Limiting on Auth Endpoint

During the council review for the GitHub OAuth integration PR:

```
@review-council: Review the GitHub OAuth integration — adds /auth/github/callback endpoint
and token exchange logic.
```

**Guardian finding:**
```
🔴 Critical | Guardian | No rate limiting on /auth/github/callback
File: app/routers/auth.py, lines 45-72
Finding: The OAuth callback endpoint accepts unlimited requests. A state parameter replay attack
or brute-force of short-lived codes is possible without a request rate limit. Combine with
callback endpoint's predictable 302 redirect pattern and this is an exploitable enumeration path.
Recommendation: Add SlowAPI rate limiting (already in dependencies) to this endpoint:
  @limiter.limit("10/minute") on the callback route.
The state parameter validation window (5 minutes, line 68) is correct — do not extend it.
```

The developer had imported SlowAPI for the login endpoint but forgotten to apply it to the callback. It was a one-line fix. The critical finding was resolved before merge.

---

## What This Example Shows

1. **N+1 queries are invisible to code review** — they only appear when the ORM generates SQL at runtime. The Performance agent looks for them as a structural pattern, not as visible code.
2. **QA regression coverage gap detection is not about failing tests** — the tests passed. QA identified that the data volume in tests was unrealistic, which is a coverage gap the tests couldn't catch themselves.
3. **Security findings surface in the council, not just in implementation** — the callback endpoint was functional and tested. The missing rate limit was a design omission, not a code error.
4. **No-frontend bootstrap correctly deactivates irrelevant agents** — and the bootstrap verification confirms the count is consistent.
