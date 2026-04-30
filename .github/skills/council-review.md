---
name: council-review
description: 'Multi-perspective review methodology with six specialized reviewer roles for comprehensive code, architecture, and UX assessment.'
---

# Council Review

## When to Use

- Reviewing code changes before merge (PR review, feature completion).
- Evaluating prompts or generated code for quality, safety, and user impact.
- Conducting periodic codebase health assessments.
- Reviewing architecture decisions or API designs from multiple angles.
- Assessing UI/UX implementations for accessibility and usability.
- Validating security posture of new features or integrations.

## The Six Perspectives

### 1. The Advocate (Strength Finder)
Identifies what is working well. Anchors the review in existing strengths before criticism begins.
- Good patterns that should be templates for new code.
- Strong test coverage areas.
- Clean API designs, clear naming, helpful documentation.

### 2. The Skeptic (Critical Analyst)
Questions assumptions and finds failure modes.
- Edge cases not covered.
- Implicit dependencies and fragile assumptions.
- Silent failures, swallowed exceptions, vague errors.
- Scale, concurrency, and configuration risks.

### 3. The Synthesizer (Pattern Integrator)
Finds the best pattern across the codebase and recommends consistency.
- Cross-module pattern comparison.
- Copy-paste deduplication opportunities.
- Conflicting implementations that should converge.
- Best-of-breed patterns that should be adopted everywhere.

### 4. The Guardian (Security Sentinel)
Focuses on attack vectors and data protection.
- Auth enforcement on all routes.
- Input validation completeness.
- Secret management and log safety.
- OAuth flow correctness (PKCE, state, scopes, token storage).
- Infrastructure hardening (TLS, CORS, headers).

### 5. The Craftsperson (Quality Architect)
Evaluates code structure and engineering excellence.
- Type safety (no unsafe type escapes, proper generics).
- Error handling consistency.
- Test quality (AAA pattern, deterministic, failure paths).
- DRY compliance, dead code, module boundaries.
- SOLID principles and dependency management.

### 6. The User Champion (UX Advocate)
Assesses the experience from the user's perspective.
- Result visibility after primary actions.
- Accessibility (WCAG AA compliance).
- Error recovery and user feedback.
- Navigation clarity and information hierarchy.
- Responsive design and loading/empty states.

## Procedure

1. **Scope  Identify what is being reviewed (files, feature, full codebase).Definition** 
2. **Advocate  Start with strengths to establish baseline quality and patterns worth keeping.First** 
3. **Parallel  Run Skeptic, Guardian, Craftsperson, and User Champion analyses.Perspectives** 
4. **Synthesizer  Compare findings across perspectives, resolve conflicts, identify cross-cutting themes.Pass** 
5. **Severity Classification:**
6. **Consensus  Unified findings with specific file references, severity, owning perspective, and recommended fix.Output**    -    -    -    - 

## Rules of Deliberation

- The Advocate always speaks first. No review starts with criticism.
- The Guardian has veto power on  security findings cannot be deprioritized below their assigned severity.security 
- The Synthesizer resolves disagreements by finding the approach that satisfies the most constraints.
- Every finding must be  "improve X" is not acceptable; "change Y in file Z at line N" is.actionable 
- The User Champion's findings on result visibility carry the same weight as functional bugs.
- No perspective can recommend weakening existing tests or security measures.
- Security findings must align with the security instructions in `.github/instructions/`
- Testing recommendations must align with the testing instructions in `.github/instructions/`

## Checklist

- [ ] Scope is clearly defined before review begins.
- [ ] Advocate has identified at least 3 strengths.
- [ ] Skeptic has examined failure modes and edge cases.
- [ ] Guardian has reviewed auth, validation, secrets, and logging.
- [ ] Craftsperson has checked type safety, patterns, and test quality.
- [ ] User Champion has verified result visibility and accessibility.
- [ ] Synthesizer has resolved conflicts and identified cross-cutting themes.
- [ ] All findings include file/line references or concrete examples.
- [ ] Severity ratings are assigned and Guardian findings are not deprioritized.
- [ ] Action items are numbered, assigned to a perspective, and implementable.
