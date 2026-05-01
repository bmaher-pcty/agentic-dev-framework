# Getting Started with the Agentic Dev Framework

> **4 steps to go from zero to your first productive agent session.**

---

## Step 1 — Understand the Framework

The agentic-dev-framework is a set of configuration files for GitHub Copilot (and compatible AI assistants) that encode engineering judgment as agent definitions, skills, and quality constraints.

**What you get:**
- **12 specialized agents** — each with a defined scope, constraints, and escalation protocol
- **25 domain skills** — procedural guidance agents invoke for specific tasks
- **5 path-scoped instructions** — automatically apply to matching files
- **6 reusable prompts** — for council reviews, audits, security review, and more
- **A 7-perspective Review Council** — Advocate, Skeptic, Synthesizer, Guardian, Craftsperson, User Champion, and Innovator

Read [`docs/PHILOSOPHY.md`](PHILOSOPHY.md) for the 5 core tenets that govern all agent behavior.

---

## Step 2 — Bootstrap for Your Project

The framework ships with `{{TOKEN}}` placeholders. Before using it, tailor it to your project.

**Run BOOTSTRAP.prompt.md in your AI assistant:**

```
Open GitHub Copilot Chat (or your AI assistant).
Attach or paste the contents of BOOTSTRAP.prompt.md.
Answer the 7 questions.
Review the grouped token table.
Approve — the assistant generates your tailored .github/ folder.
```

See [`BOOTSTRAP.prompt.md`](../BOOTSTRAP.prompt.md) for the full wizard.

**What bootstrap produces:**
- `.github/copilot-instructions.md` with your actual stack
- All 12 agents configured for your project type
- Instruction files with your actual glob patterns
- `docs/FRAMEWORK_SETUP.md` — a persistent record of your configuration

---

## Step 3 — Verify the Bootstrap Output

After bootstrapping, run these checks:
- No `{{TOKEN}}` patterns remain in `.github/` files (the CI gate in `docs/templates/ci-quality-gates.template.yaml` automates this)
- `.github/agents/` contains your active agents (deactivated ones have stub files)
- `docs/FRAMEWORK_SETUP.md` exists and lists your active agents and resolved tokens

---

## Step 4 — Invoke Your First Agent

```
# In GitHub Copilot Chat:

@engineer: Add input validation to the user creation endpoint

@architect: Design the session management flow

@review-council: Review this PR before I merge it

@innovator: We've been building the same way for 6 months. What are we assuming that might be wrong?
```

**For the first PR merge**, always run `@review-council` — it establishes the first baseline for `docs/project-intelligence.md`.

**First council review checklist:**
- [ ] Bootstrap complete (`docs/FRAMEWORK_SETUP.md` exists)
- [ ] At least one `@engineer` session completed
- [ ] Run: "Review this PR using the full 7-perspective council"
- [ ] After review: populate `docs/project-intelligence.md` with the first findings
- [ ] File your first ADR if any High-Stakes architectural decision was made

---

## Next Steps

- Read [`README.md`](../README.md) for the full agent and skills reference
- Read [`TOKENS.md`](../TOKENS.md) if you need to understand any `{{TOKEN}}` placeholder
- Run [`BOOTSTRAP.prompt.md`](../BOOTSTRAP.prompt.md) to configure the framework
- Review [`docs/PHILOSOPHY.md`](PHILOSOPHY.md) when you want to understand WHY a rule exists
- Add [`docs/templates/ci-quality-gates.template.yaml`](templates/ci-quality-gates.template.yaml) to your project's CI for structural enforcement
