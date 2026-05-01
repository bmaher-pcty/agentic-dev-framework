# agentic-dev-framework

A lean, technology-agnostic Copilot framework with 5 specialized agents and 8 domain skills.

## Why This Exists

Default AI assistants say things like: *"I've updated the function — it should work now."*

This framework makes them say:

```
## Verification Summary
- auth.ts: VERIFIED-AUTOMATED (test: auth.test.ts — 12 tests pass)
- middleware.ts: VERIFIED-MANUAL (ran: npm test, observed: all 47 tests green)
- callback.ts: REQUIRES-HUMAN-VERIFICATION (OAuth redirect — cannot simulate without provider)
```

That difference — **honest labeling of what was actually verified** — is the framework's core value. Everything else supports it.

---

## 2-Minute Drop-In (No Bootstrap Required)

Copy `AGENTS.md` from this repo into your project root. Open GitHub Copilot Chat and type:

```
@engineer: Add rate limiting to the login endpoint
```

**What you get:**

```
## Verification Summary
- src/routes/auth.ts: VERIFIED-AUTOMATED (test: auth.test.ts — rate limit returns 429 after 5 attempts)
- src/middleware/rateLimit.ts: VERIFIED-MANUAL (ran: npm test, observed: 23/23 tests green)
- integration flow: REQUIRES-HUMAN-VERIFICATION (needs running Redis instance to verify limit persistence)
```

That's the difference. The model can't just say "done" — it must label what it actually verified and flag what it didn't.

**Solo developer?** See [`docs/examples/solo-starter/AGENTS.md`](docs/examples/solo-starter/AGENTS.md) for the lightweight single-file version.

---

## Full Setup (10 Minutes)

For path-scoped security rules, testing gates, and individual agent files:

```bash
cp -r agentic-dev-framework/.github /path/to/your-project/
cp agentic-dev-framework/BOOTSTRAP.prompt.md /path/to/your-project/
cp agentic-dev-framework/TOKENS.md /path/to/your-project/
```

Open `BOOTSTRAP.prompt.md` in Copilot Chat and answer 7 questions. The AI fills in the rest.

After bootstrap: `grep -r '{{' .github/` should return **zero matches**.

---

## 5 Agents

| Agent | When to use | Invoke |
|-------|-------------|--------|
| **Engineer** | Implementing, fixing bugs, verifying behavior | `@engineer` |
| **System Architect** | API design, data models, feature scoping | `@architect` |
| **Review Council** | Pre-PR review, architecture audits | `@review-council` |
| **Veteran QA** | Test planning, release readiness | `@qa` |
| **Innovator** | Pre-commitment research, problem reframing | `@innovator` |

## 8 Skills

| Skill | Use for |
|-------|---------|
| `#council-review` | 7-perspective review methodology |
| `#task-triage` | 🟢/🟡/🔴 risk classification |
| `#testing` | Verification patterns and coverage |
| `#security` | Auth, secrets, input validation |
| `#api-design` | REST contracts and versioning |
| `#error-handling` | Error structures and logging |
| `#pull-request-readiness` | Pre-PR cleanup and security sweep |
| `#data-modeling` | Schema design and migrations |

---

## What Makes This Different

1. **Verification labels** — `VERIFIED-AUTOMATED` / `VERIFIED-MANUAL` / `ASSUMED-UNTESTED` / `REQUIRES-HUMAN-VERIFICATION`. The model can't just say "done."
2. **Completion gate** — the model continues working if verification hasn't passed, instead of declaring success on a broken app.
3. **Task triage** — 🟢 trivial / 🟡 standard / 🔴 high-stakes; different depth of review for different risk levels.
4. **Honest council** — the Review Council's Guardian cannot be talked out of a security finding. It requires a documented ADR to defer — not just pushback.
5. **Invisible success is a defect** — the User Champion in every council review treats "feature works but result is invisible" as blocking.

---

## Philosophy (5 Rules)

1. Keep only what measurably changes model output. Everything else is overhead.
2. Work is done when it's verified in the user-visible path — not when code is committed.
3. Security findings cannot be silently downgraded. Document the disposition or fix it.
4. The first viable solution is rarely the best. Use the Innovator before committing to architecture.
5. Honest reporting of uncertainty is a feature, not a weakness.

---

## Directory Layout

```
AGENTS.md                          # Drop-in single-file starter (start here)
BOOTSTRAP.prompt.md                # 7-question setup wizard
TOKENS.md                          # Token reference
.github/
  copilot-instructions.md          # Global rules
  agents/                          # 5 agent files
  skills/                          # 8 skill files
  instructions/
    testing.instructions.md        # Verification labels + completion gate
    security.instructions.md       # Auth, secrets, infra rules
    branching.instructions.md      # Branch workflow
  prompts/
    council-review.prompt.md
    pr-readiness.prompt.md
    security-review.prompt.md
    smoke-verification.prompt.md
    repo-cleanup.prompt.md
docs/
  templates/
    ADR.template.md
    ROADMAP.template.md
  examples/
    fastapi-react/                 # Concrete adoption example
```

## Framework Version
agentic-dev-framework v2.0.0 ("Lean Machine")
