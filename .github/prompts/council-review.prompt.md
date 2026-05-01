---
name: "Council Review"
description: "Run a multi-perspective review with seven specialized reviewers (Advocate, Skeptic, Synthesizer, Guardian, Craftsperson, User Champion, Innovator) on specific code, files, or features."
argument-hint: "Specify files, features, or a PR to review. Use 'full codebase' for a comprehensive audit."
---

Convene the Review Council to evaluate the specified code or feature from seven perspectives.

## Council Members

1. **The Advocate** — What is strong, well-built, and worth preserving?
2. **The Skeptic** — What assumptions are fragile? What fails at 2 AM?
3. **The Synthesizer** — Where is the best pattern, and where should it be applied everywhere?
4. **The Guardian** — What are the attack vectors, secret risks, and auth gaps?
5. **The Craftsperson** — Is this maintainable, testable, DRY, and properly typed?
6. **The User Champion** — Can users find results, recover from errors, and navigate clearly?
7. **The Innovator** — Reads all six perspectives first. Reframes problems, challenges shared assumptions, proposes alternatives not considered by the other six. Speaks last.

## Procedure

**Step 0: Task Triage**
Before convening perspectives, invoke `#task-triage` to classify the change. The classification determines which perspectives are activated. Document the classification in the council output header: "Triage: [Trivial | Standard | High-Stakes] — [brief reason]"

1. Identify the review scope from the user's input (files, feature area, or full codebase).
2. Read the relevant code, tests, and configuration.
3. Run each perspective analysis in the order above.
4. Synthesize findings into a unified assessment.
5. Classify each finding by severity (🔴 Critical / 🟠 High / 🟡 Medium / 🔵 Low — see `.github/skills/council-review.md`).
6. Produce prioritized action items with specific file references and recommended fixes.

## Output

1. Council Summary (one paragraph).
2. Advocate Highlights (top strengths with file references).
3. Critical Findings table (Severity | Perspective | Finding | File | Fix).
4. Security Assessment section.
5. UX Assessment section.
6. Prioritized Action Items (numbered, with severity and owner).

7. **Project Intelligence Update** — After all findings are synthesized, produce a Project Intelligence Update block. See `council-review.instructions.md` "Output Format Requirements" section 6 for required format. Must include: new anti-patterns, locked decisions, coverage hot spots, open Innovator experiments. If nothing new, write: "No updates — intelligence file current."

## Mandatory Post-Review Actions

After delivering the full council review output, the Review Council agent must execute these five steps in order:

1. **Update `docs/project-intelligence.md`**: Add all new anti-patterns, locked decisions, and coverage hotspots surfaced in this review. Increment the front-matter count fields (`anti_pattern_count`, `locked_decision_count`, etc.) to match. Set `last_updated` to today's date and `last_updated_by` to "Review Council".

2. **Check for recurrences**: Compare findings in this review against existing entries in `docs/project-intelligence.md`. If a known anti-pattern has recurred, escalate its record in the intelligence file: append "(RECURRING — [date])" to the pattern name and note the recurrence date.

3. **File ADRs for High-Stakes decisions**: If any finding requires an architectural decision (new integration pattern, security approach change, data model revision), create `docs/adr/ADR-[N]-[title].md` using `docs/templates/ADR.template.md`. Reference the ADR in the intelligence file's Locked Architectural Decisions section.

4. **Update open handoffs**: Check `docs/open-handoffs.md` for any OPEN handoffs that this review resolves. Update their status to `RESOLVED` and fill in the Resolution field with a one-line summary.

5. **Deliver the Intelligence Update summary** as the final block of the review output, formatted for copy-paste into `docs/project-intelligence.md` if the agent cannot directly edit files. Use the header `## Project Intelligence Update — [date]` so the recipient can locate and paste it precisely.

> If the agent cannot write files in this session, every step above must still be reported as a formatted, copy-pasteable block in the review output. The human reviewer is responsible for applying them before the PR merges.

## Rules

- Every finding must reference a specific file or line.
- Advocate speaks first; no review opens with criticism.
- Guardian findings cannot be deprioritized.
- Do not recommend weakening tests or security measures.
- Reference `.github/skills/council-review.md` for methodology details.
- Apply the path-scoped security and testing instructions in `.github/instructions/` when evaluating findings.
