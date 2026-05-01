# TaskFlow — FastAPI + React Bootstrap Reference

> **This is example output from `BOOTSTRAP.prompt.md`.** It shows exactly what the agentic-dev-framework produces when bootstrapped for a Python/FastAPI + React/TypeScript project.

> **Do not copy these files into your project.** Run `BOOTSTRAP.prompt.md` instead — it generates files tailored to your stack, not TaskFlow's. The answers that produced this output are in `BOOTSTRAP_ANSWERS.md`.

---

## What's In This Example

| File | What It Demonstrates |
|------|---------------------|
| `BOOTSTRAP_ANSWERS.md` | The 7 bootstrap questions answered for TaskFlow; complete token resolution map |
| `.github/agents/engineer.agent.md` | Engineer agent with all tokens resolved to TaskFlow values |
| `.github/instructions/testing.instructions.md` | Testing instructions with real Python/TypeScript glob patterns |

---

## Token → Value Reference

Key resolutions for this example (full table in `BOOTSTRAP_ANSWERS.md`):

| Token | TaskFlow Value | Your Project Would Use |
|-------|---------------|----------------------|
| `{{SERVER_FRAMEWORK}}` | FastAPI | Express, Rails, Spring Boot, etc. |
| `{{PRIMARY_LANGUAGE}}` | Python | TypeScript, Go, Java, etc. |
| `{{SMOKE_COMMAND}}` | `make smoke` | `npm run smoke`, `pytest tests/smoke/`, etc. |
| `{{SOURCE_CODE_PATHS}}` | `backend/**/*.py,frontend/src/**/*.ts,frontend/src/**/*.tsx` | Your actual glob patterns |
| `{{UNIT_TEST_FRAMEWORK}}` | pytest | Jest, Vitest, go test, etc. |
| `{{A11Y_TESTING_LIB}}` | @axe-core/playwright | pa11y, axe-core, etc. |

---

## Key Differences from Default Framework

The resolved files differ from the framework defaults in these ways:

1. **`applyTo` patterns** — use Python + TypeScript globs instead of `{{SOURCE_CODE_PATHS}}`
2. **`make smoke`** — replaces `{{SMOKE_COMMAND}}` everywhere
3. **`pytest`** — replaces `{{UNIT_TEST_FRAMEWORK}}` in test guidance
4. **`backend/src`** and **`frontend/src`** — replace `{{SERVER_SOURCE_DIR}}` and `{{CLIENT_SOURCE_DIR}}`
5. **GitHub OAuth** — replaces `{{AUTH_PROVIDER}}` in security sections

---

## How to Run Bootstrap for Your Own Project

```
1. Open GitHub Copilot Chat (or your AI assistant)
2. Attach or paste BOOTSTRAP.prompt.md from the framework root
3. Answer the 7 questions with YOUR project's values
4. Review the grouped token table
5. Approve — the assistant generates your tailored .github/ folder
```

The bootstrap wizard infers most values from your language and stack choices. You only need to provide project name, domain, and any values the wizard can't infer (custom commands, unusual directory structures).
