---
applyTo: "{{SERVER_SOURCE_DIR}}/**/*.ts,{{SERVER_SOURCE_DIR}}/**/*.js,{{CLIENT_SOURCE_DIR}}/src/**/*.ts,{{CLIENT_SOURCE_DIR}}/src/**/*.tsx,{{CLIENT_SOURCE_DIR}}/integration_tests/**/*.ts,.github/agents/**/*.md,.github/skills/**/*.md,.github/prompts/**/*.md"
---

# Council Review Instructions

Use these instructions when the Review Council agent is invoked or when a council-style multi-perspective review is requested.

## Structural Requirements

1. Every council review must include all six perspectives: Advocate, Skeptic, Synthesizer, Guardian, Craftsperson, User Champion.
2. The Advocate always presents first. Reviews must not open with criticism.
3. Each perspective section must contain at least two concrete findings with file references.
4. The Guardian and User Champion sections must always appear as dedicated sections in the output, never folded into other perspectives.
5. Findings without specific file paths, line numbers, or concrete examples are not valid findings.

## Severity Classification

1. 🔴 **Critical** — production-breaking, security, or false-complete claim.
2. 🟠 **High** — significant defect, missing verification, or quality regression.
3. 🟡 **Medium** — maintainability, clarity, or minor UX gap.
4. 🔵 **Low** — polish or future enhancement.

This legend mirrors the one in `.github/skills/council-review.md`; both must stay in sync.
## Conflict Resolution

1. When perspectives disagree, the Synthesizer resolves by finding the approach satisfying the most constraints.
2. Security findings from the Guardian cannot be downgraded in severity by other perspectives.
3. User Champion findings about invisible success states or missing result surfaces carry the same weight as functional defects.
4. The Craftsperson defers to existing patterns unless a measurable improvement is demonstrated.

## Output Format Requirements

1. Start with a one-paragraph Council Summary assessing overall quality.
2. Follow with Advocate Highlights (minimum 3 strengths).
3. Present Critical Findings in a table: Severity | Perspective | Finding | File | Recommendation.
4. Dedicate separate sections to Security Assessment and UX Assessment.
5. End with numbered Prioritized Action Items (severity, perspective, specific fix).

## Integration With Existing Standards

1. Security findings must align with the path-scoped security instructions in `.github/instructions/`.
2. Testing recommendations must align with the path-scoped testing instructions in `.github/instructions/`.
3. UX findings must reference `.github/skills/ui-principles.md` and `.github/skills/ui-component-design.md`.
4. Code quality findings must reference `.github/skills/static-typing-practices.md` and `.github/skills/error-handling.md`.
