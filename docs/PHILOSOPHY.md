# Framework Philosophy

> This is a reference document — read it to understand why rules exist. If you are new to the framework, start with [`docs/GETTING_STARTED.md`](GETTING_STARTED.md).

## Why This Framework Exists

AI coding assistants are powerful, but unconstrained they drift toward:
- Declaring work "done" when only the happy path compiles.
- Skipping user-visible verification in favor of unit test green.
- Producing inconsistent patterns across sessions.
- Missing security, accessibility, and quality concerns.
- Breaking the app and stopping at "implemented."

This framework solves those problems by encoding **engineering judgment** directly into the agent configuration. Agents don't guess what "done" means — it's defined for them.

---

## Core Principles

### 1. User-Visible Verification Is Mandatory

A feature is not done because the code compiles or the unit tests pass. A feature is done when:
- The user can navigate to the result.
- The output is visible, accurate, and inspectable.
- Failure states show actionable messages, not empty screens.

Every agent is constrained by this rule. The Veteran QA and Engineer agents treat "green tests, invisible output" as a blocking defect.

### 2. No Broken Handoff

If a change leaves the application broken, the work is not complete. The agent must continue until:
- The application is reachable and functional, OR
- A concrete external blocker is identified and surfaced.

"Implemented" is not a valid stopping point if the app is down.

### 3. Security Is Non-Negotiable

The Guardian perspective in every Review Council must not be overridden. Security findings must not be deprioritized, softened, or deferred without an explicit decision and documented ADR.

In practice, "non-negotiable" means that any proposal to defer or downgrade a security finding requires a human to make that decision explicitly and document it in an ADR. AI agents are configured to resist this — but the human in the loop is the ultimate enforcement mechanism.

### 4. Quality Bars Are Additive

Once established, quality bars only go up. Test coverage, accessibility compliance, security posture, and documentation accuracy never regress without an explicit user decision.

### 5. Agents Own Their Scope

Each agent has a defined scope and is responsible for that scope. When a concern belongs to a different agent, escalation is explicit. This prevents:
- Engineers making UX decisions they shouldn't own.
- QA agents implementing features.
- PMs overriding security constraints.

### 6. Scripts and Tooling Are Centralized

All operational scripts live in one place (the scripts directory). Documentation and CI reference task runner targets, not script paths. This prevents the "which script do I run?" problem as repositories evolve.

### 7. Operator Setup Is Private

Documentation that explains how to set up credentials, register OAuth apps, or configure tenant-specific values must never be committed. It belongs in `docs/local/`, which is gitignored. The `.env.example` file is the only tracked record of which variables exist.

---

## The 7-Perspective Review Model

The Review Council methodology is the quality capstone of this framework. Seven perspectives ensure no concern is invisible:

| Perspective | Ensures |
|-------------|---------|
| **Advocate** | Good patterns are recognized and preserved |
| **Skeptic** | Edge cases, failure modes, and fragile assumptions are found |
| **Synthesizer** | Best patterns spread across the codebase |
| **Guardian** | Security vulnerabilities and secret exposure are blocked |
| **Craftsperson** | Code quality, type safety, and test coverage are maintained |
| **User Champion** | Users can find outputs, recover from errors, and navigate clearly |
| **Innovator** | Reframes the problem; challenges shared assumptions; speaks last |

No perspective outranks another except: the Guardian's security findings always take final priority.

---

## Token-Based Abstraction

The framework uses `{{TOKEN}}` placeholders instead of hardcoded technology names. This makes it:
- **Portable** — use it with any language, framework, or toolchain.
- **Honest** — instructions explicitly state what technology they assume.
- **Upgradeable** — when you change your stack, update one token and all instructions stay consistent.

See `TOKENS.md` for the full token reference.

---

## What This Framework Is Not

- **Not a code generator** — agents guide and verify; they don't generate production code on autopilot.
- **Not a process enforcement tool** — this is guidance, not a CI gate. Teams adopt it because it makes work better, not because they're forced to.
- **Not a substitute for judgment** — agents escalate when they're uncertain. Human judgment resolves ambiguity that agents cannot.
- **Not prescriptive about technology** — the framework works with any stack. Token replacement is the mechanism for making it your own.

---

## Framework Version
agentic-dev-framework v1.1.0
