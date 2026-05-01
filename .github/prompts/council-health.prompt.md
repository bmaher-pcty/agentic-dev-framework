---
name: Council Health Check
description: Audits the framework's own effectiveness by examining project-intelligence.md, open-handoffs.md, ADR coverage, and council output quality patterns. Detects cargo-cult adoption and produces concrete improvement recommendations.
user-invocable: true
argument-hint: "[optional: path to project root, defaults to current directory]"
---

# Framework Health Check

> Run quarterly or after 5+ council reviews to detect whether the framework is producing genuine engineering value or becoming a compliance ritual.

## What This Prompt Does

Reads the living framework documents — `project-intelligence.md`, `open-handoffs.md`, and `docs/adr/` — and assesses whether the framework is being used to accumulate knowledge or just to generate review outputs.

---

## Procedure

**Step 1 — Intelligence File Health**

Read `docs/project-intelligence.md`.

Check each section:
- **Known Anti-Patterns**: Are there entries? If all sections still say "No patterns documented yet," the intelligence file has never been updated after a council review. This is Finding H-01: Intelligence file is write-only — zero council reviews have populated it.
- **Locked Architectural Decisions**: Are there entries? Empty = no architectural decisions documented despite the framework being in use.
- **Coverage Hot Spots**: Are there entries?
- **Open Innovator Experiments**: Are there entries? If yes — what is the status? Any experiments still `PROPOSED` after more than one sprint are likely stale.

**Step 2 — Handoff Queue Health**

Read `docs/open-handoffs.md`.

Count:
- Total `[OPEN]` entries
- `[OPEN]` entries with no date
- `[OPEN]` entries that appear to be >7 days old based on context

If >5 OPEN entries: Finding H-02: Handoff queue is above the healthy threshold. Each entry should be reviewed — either RESOLVED or DEFERRED with explanation.

If any OPEN entry has no resolution progress indicator: Finding H-03: Stale open handoff — escalation was logged but has not been progressed.

**Step 3 — ADR Coverage**

Check if `docs/adr/` directory exists. If not: Finding H-04: No ADR directory — architectural decisions are not being documented.

If `docs/adr/` exists but is empty: Finding H-05: ADR directory exists but contains no records — High-Stakes council reviews have not produced ADRs.

If ADRs exist: check for any with `Status: Proposed` older than 2 sprints — Finding H-06: Stale proposed ADR.

**Step 4 — Recurring Pattern Detection**

If `docs/project-intelligence.md` Known Anti-Patterns section has entries:
- Are any of the same patterns mentioned across multiple entries or appearing repeatedly? This suggests the pattern is structural, not incidental.
- Finding H-07: Recurring anti-pattern not yet resolved — appears in [N] entries. Recommend creating a targeted fix or ADR.

**Step 5 — Innovator Output Quality**

Check `docs/project-intelligence.md` Open Innovator Experiments section.
- Are any experiments `RUNNING` with no finding yet? If the experiment is still running and it's been >2 sprints: Finding H-08: Long-running Innovator experiment — needs a result or kill decision.
- Are there zero experiments? Finding H-09: Zero Innovator experiments documented — Innovator may not be producing actionable spikes in council reviews.

---

## Output Format

```markdown
# Framework Health Report — [Date]

## Health Summary
[One paragraph overall assessment: is the framework being used to accumulate knowledge or just to generate review outputs?]

## Findings

| ID | Severity | Finding | Recommendation |
|----|----------|---------|----------------|
| H-01 | 🔴/🟠/🟡 | [description] | [action] |
...

## Detailed Findings
[For each finding above, one paragraph with specific evidence from the files examined]

## Top 3 Actions to Improve Framework Value
1. [Most important action — concrete, specific, doable in 1 session]
2. [Second action]
3. [Third action]

## Next Health Check
Recommended: [date/sprint — suggest 4-8 weeks from now]
```

---

## Rules

- Do NOT fabricate findings. Only report what is actually observed in the files.
- If a file doesn't exist yet (e.g., `docs/adr/`), report its absence as a finding — do not create it during the health check.
- The health check is diagnostic, not corrective. Produce findings; leave implementation to the Engineer agent.
- A clean health report (zero findings) is a positive signal — report it explicitly: "Framework health: clean. No corrective actions needed."
