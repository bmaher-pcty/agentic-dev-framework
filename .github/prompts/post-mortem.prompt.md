---
name: "Post-Mortem Capture"
description: "Capture session failures, surprises, and incidents into docs/project-intelligence.md. Run after any bug, unexpected council finding, session where something went wrong, or any time the framework didn't prevent something it should have."
argument-hint: "Describe what failed or surprised you. The prompt will generate structured entries ready to paste into docs/project-intelligence.md."
user-invocable: true
---

# Post-Mortem Capture

## Purpose

`docs/project-intelligence.md` is the highest-leverage file in the repository. A well-populated file produces project-aware, context-rich agent responses. An empty or stale file produces generic advice that ignores your project's specific failure history.

This prompt converts session failures and surprises into structured entries for that file. It takes 2 minutes. It compounds across every future session — every agent that reads the file benefits from what you learned today.

## When to Run This

- After a bug reaches production that the framework should have caught.
- After a council review surfaces a finding that recurred from a previous session.
- After a session where `@engineer` declared work done but it was actually broken.
- After any incident that reveals a gap in your project's documented patterns.
- After completing a significant feature — capture the architectural decisions before context fades.

## Procedure

Answer these four questions. Be specific — vague answers produce vague intelligence entries.

---

**Question 1:** What failed or surprised you in this session?

Describe: What did you expect to happen? What actually happened? What was the gap?

*(Example: "Expected @engineer to catch the missing auth check on the new endpoint. It declared VERIFIED-MANUAL but didn't actually test the unauthenticated path.")*

---

**Question 2:** Which framework agent or skill, if it had been invoked earlier or more carefully, would have caught this?

*(Example: "Guardian in the Review Council. If I had run a High-Stakes council review before declaring done, the missing auth check would have been a 🔴 Critical finding.")*

---

**Question 3:** Is this a one-time incident or a recurring pattern?

Has something similar happened before — even partially, even in a different area of the codebase?

*(Example: "This is the second time we've shipped an endpoint without testing the unauthenticated path.")*

---

**Question 4:** What is the one-sentence rule that would prevent this from recurring?

State it as a constraint, not a reminder.

*(Example: "All new API endpoints must have an explicit test for the unauthenticated path before the PR is opened.")*

---

## Output Format

After you answer the four questions, generate copy-paste-ready rows for `docs/project-intelligence.md` in this format:

```markdown
## Post-Mortem Capture — [date]

### Paste into: Known Anti-Patterns
**[Pattern name derived from Q1]:** [Where it appears — be specific to this codebase] — [Why it's a problem, from Q2 and Q4] — [Preferred alternative: the rule from Q4]

### Paste into: Locked Architectural Decisions
*(N/A — or add a decision if Q1 reveals a structural pattern that should be locked)*
**[Decision name]:** [Rationale] — [Date decided] — [ADR link if created]

### Paste into: Current Test Coverage Hot Spots
*(N/A — or add a coverage gap if Q1 reveals untested paths)*
`[path/to/file.ext]` — [Why under-tested, from Q3] — [What's missing]

### Paste into: Past Incident Patterns
**[Incident summary from Q1]** | Root cause: [from Q2] | Agent that would have caught it: [from Q2] | Resolution: [from Q4]
```

Mark sections as "N/A" if the incident doesn't apply to that section. Do not leave rows blank — if a section doesn't apply, state why explicitly so future reviewers understand the omission was intentional.

---

## After Pasting

1. Open `docs/project-intelligence.md`.
2. Paste each section's row into the appropriate table.
3. Increment the front-matter counter (`anti_pattern_count`, `coverage_hotspot_count`, etc.) to match the new row count.
4. Update `last_updated` to today's date.
5. Commit: `git add docs/project-intelligence.md && git commit -m "docs(intelligence): capture post-mortem from [brief description]"`

The 2-minute investment compounds. Every future agent session benefits.
