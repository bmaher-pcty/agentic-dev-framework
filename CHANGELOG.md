# Changelog

All notable changes to the agentic-dev-framework are documented here.

## [Unreleased]

### Added
- ADR template (`docs/templates/ADR.template.md`) for documenting architectural decisions from High-Stakes reviews
- `council-health.prompt.md` prompt for quarterly framework effectiveness auditing
- `docs/GETTING_STARTED.md` — 4-step orientation guide for new adopters
- `docs/templates/FRAMEWORK_SETUP.md.template.md` — structural template for bootstrap-generated setup docs
- `#task-triage` added to skill listings in `copilot-instructions.md` and `README.md` (skill count: 22 → 23)
- Micro Council exception in `council-review.instructions.md` — Trivial tasks permitted to use 4-perspective Micro Council; Guardian escalation rule added
- Step 0.5 "Read Project Intelligence" in `council-review.md` procedure; matching checklist item added
- Project Intelligence Update output item added to `council-review.prompt.md`
- `codebase-audit.prompt.md`: 7 perspectives (was 6), Innovator section, Innovator Reframe output item, Project Intelligence Update output item

### Changed
- `copilot-instructions.md`: skill count updated from 22 to 23; `## 22 Domain Skills` → `## Domain Skills`; Innovation skills renumbered 21/22/23
- `README.md`: skill count updated from 22 to 23; `## The 22 Skills` → `## Skills`; task-triage row added to skills table
- Framework version bumped to v1.1.0 in `README.md` and `docs/PHILOSOPHY.md`

---

## [1.1.0] — Round 1 Enhancements

### Added
- `#task-triage` skill (`task-triage.md`) — risk-calibrated review depth classification; determines Micro Council vs. full 7-perspective council
- `docs/project-intelligence.md` — living document for known anti-patterns, locked architectural decisions, coverage hot spots, and open Innovator experiments
- `docs/open-handoffs.md` — SBAR-format escalation log template for cross-agent handoffs
- `docs/templates/ci-quality-gates.template.yaml` — CI gates template including token-detection scan and handoff-check gate
- `docs/templates/ROADMAP.template.md` — phase-structured roadmap template
- `CHANGELOG.md` — this changelog
- Forced-choice verification schema added to `testing.instructions.md` and `engineer.agent.md`
- Solo Developer profile in `BOOTSTRAP.prompt.md` — 5 default agents, Micro Council as default review depth, full quality guarantees preserved
- Grouped token table in BOOTSTRAP Phase 3 — tokens grouped by category with confidence levels
- `docs/FRAMEWORK_SETUP.md` generation added to BOOTSTRAP Phase 5 (step 7)
- Escalation Protocol and `docs/open-handoffs.md` reference added to `copilot-instructions.md`
- 7th perspective (Innovator) added to `review-council.agent.md`
- ADR requirement for deferred Guardian findings documented in `council-review.instructions.md`
- Project Intelligence Update as mandatory council output in `council-review.instructions.md`

### Changed
- `BOOTSTRAP.prompt.md` redesigned from 47-question process to 7-question smart wizard
- `review-council.agent.md` updated with full Innovator perspective definition and validity rules

[1.1.0]: https://github.com/bmaher-pcty/agentic-dev-framework/compare/v1.0.0...v1.1.0

---

## [1.0.0] — Initial Release

### Added
- 11 specialized agents: Engineer, Review Council (7 perspectives), Thoughtful Product Manager, System Architect, Bold UX Designer, Technical Writer, Veteran QA, Accessibility, DevOps/Infrastructure, Performance, Innovator
- 22 domain skills across Backend, Frontend, Infrastructure, and Cross-Functional categories
- 5 path-scoped instruction files: testing, security, branching, container/infrastructure, council-review
- 6 reusable prompts: council-review, codebase-audit, pr-readiness, security-review, smoke-verification, repo-cleanup
- Smart 7-question BOOTSTRAP.prompt.md wizard (replaces original 47-question process)
- TOKENS.md manifest with 50+ tokens, Source classification (Required / Inferred / Optional)
- docs/PHILOSOPHY.md — 5 core tenets
- docs/GETTING_STARTED.md — 4-step orientation guide

### Framework Philosophy
- Invisible success is a defect
- The completion gate is sacred
- Security findings are never downgraded
- Measure before optimizing
- Blocked is better than wrong

[1.0.0]: https://github.com/bmaher-pcty/agentic-dev-framework/releases/tag/v1.0.0

