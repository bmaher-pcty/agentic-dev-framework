---
name: council-review
description: 'Multi-perspective review methodology with seven specialized reviewer roles for comprehensive code, architecture, and UX assessment.'
---

# Council Review

## When to Use

- Reviewing code changes before merge (PR review, feature completion).
- Evaluating prompts or generated code for quality, safety, and user impact.
- Conducting periodic codebase health assessments.
- Reviewing architecture decisions or API designs from multiple angles.
- Assessing UI/UX implementations for accessibility and usability.
- Validating security posture of new features or integrations.

## Council Modes

Choose the mode based on task triage level (see `#task-triage`).

### Standard Mode *(default for 🟡 Standard and 🔵 Trivial)*
All perspectives generated in a single pass. Faster, more internally consistent. Appropriate for routine feature reviews, refactors, and documentation changes.

### Structured Independence Mode *(recommended for 🔴 High-Stakes)*
**Sequential blinding:** write each perspective independently without reading prior ones. Use the order defined in `.github/prompts/council-review.prompt.md`. Produces more divergent findings. Worth the extra time for security/auth changes, architectural decisions, and anything you must trust.

### Temporal Separation Mode *(free, underused)*
Run Guardian at code-write time — before you've convinced yourself it's correct. Run Skeptic 24 hours later when context has faded. Run User Champion right before demo or release. Same model, genuinely different priming. Costs only discipline.

### Multi-Model Mode *(for findings you must stake trust on)*
Run the same council prompts in two different AI assistants for genuine independence. 3× slower. Reserve for High-Stakes security reviews and architectural decisions before large refactors.

## The Seven Perspectives

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

### 7. The Innovator (Alternative Lens)
Speaks last. Challenges the frame of the problem itself.
- Asks whether a fundamentally different approach would have served better — not a better implementation of the current approach, but a different approach entirely.
- Surfaces cross-domain patterns or prior art the other six perspectives may have missed, with source tier and confidence level.
- Proposes a concrete timeboxed experiment (see `#experimental-design`) if meaningful uncertainty remains after the other perspectives have spoken.
- Finding is **invalid** if it merely restates a concern already raised by another perspective — the Innovator must add a genuinely new framing, not reinforce an existing one.
- Finding is **invalid** if it proposes an alternative with no plausible implementation path — every alternative must have a concrete next step.
- If no better alternative is apparent, the Innovator must state which assumption they challenged, what they found when they investigated it, and why it held. This is still a valuable contribution: it confirms that the current approach survived scrutiny from a creative perspective.

## Procedure

0. **Triage** — Classify the task using `#task-triage`. Document the classification in the council output header. If Trivial, proceed with Micro Council (4 perspectives). If Standard or High-Stakes, proceed with full 7-perspective council.
1. **Scope Definition** — Identify what is being reviewed (files, feature, full codebase).
2. **Advocate First** — Start with strengths to establish baseline quality and patterns worth keeping.
3. **Parallel Perspectives** — Run Skeptic, Guardian, Craftsperson, and User Champion analyses.
4. **Synthesizer Pass** — Compare findings across perspectives, resolve conflicts, identify cross-cutting themes.
5. **Innovator Last** — After all other perspectives have spoken, the Innovator presents an alternative framing or cross-domain insight not raised by any other perspective. If no alternative is apparent, the Innovator documents the assumption challenged and why it held.
6. **Severity Classification** — Rate each finding using the legend below.
7. **Consensus Output** — Unified findings with specific file references, severity, owning perspective, and recommended fix.

## Severity Classification

- 🔴 **Critical** — production-breaking, security, or false-complete claim.
- 🟠 **High** — significant defect, missing verification, or quality regression.
- 🟡 **Medium** — maintainability, clarity, or minor UX gap.
- 🔵 **Low** — polish or future enhancement.

This is the canonical severity legend for all council reviews. All other files reference this definition.

## Rules of Deliberation

- The Advocate always speaks first. No review starts with criticism.
- The Innovator always speaks last. Their contribution must be genuinely new — a restatement of another perspective's concern is not valid.
- The Guardian has veto power on security — security findings cannot be deprioritized below their assigned severity.
- The Synthesizer resolves disagreements by finding the approach that satisfies the most constraints.
- Every finding must be actionable — "improve X" is not acceptable; "change Y in file Z at line N" is.
- The User Champion's findings on result visibility carry the same weight as functional bugs.
- No perspective can recommend weakening existing tests or security measures.
- Security findings must align with the security instructions in `.github/instructions/`.
- Testing recommendations must align with the testing instructions in `.github/instructions/`.

## Innovator: Example Valid Findings

These examples illustrate what distinguishes a valid Innovator finding from a restatement of concerns already raised by other perspectives.

**Example 1 — Valid: Alternative Framing**
> *Context: The Guardian flagged insufficient rate limiting on the authentication endpoint. The Craftsperson noted the implementation doesn't follow the existing middleware pattern.*
>
> **Innovator finding:** "Both findings assume rate limiting must be implemented at the application layer. But most production stacks already have a reverse proxy in front of the API. Rate limiting in the proxy layer would catch all traffic — including health check abuse and bot scanning — before it reaches the application. The application-layer rate limiter would only catch authenticated traffic. Consider whether the architectural responsibility belongs in the proxy, not the route handler. If it does, the Guardian's concern is fully addressed without adding application code, and the Craftsperson's middleware pattern concern becomes irrelevant."
>
> *Why this is Valid:* It presents a structurally different solution layer (proxy vs. application) that neither the Guardian nor the Craftsperson considered, and it would fully resolve both concerns if correct.

**Example 2 — Valid: Assumption Challenged and Held**
> *Context: The Advocate praised the new caching strategy. The Skeptic questioned its staleness window. The Synthesizer proposed a shorter TTL.*
>
> **Innovator finding:** "The Skeptic and Synthesizer both assume the correct solution is to tune the cache TTL. But the underlying assumption is that users need real-time data for this feature. The product spec says this is a reporting dashboard refreshed daily. If the domain requirement is 'data current as of yesterday,' the 'staleness' concern isn't a technical problem — it's a product requirement that was never stated. Before shortening the TTL, confirm with the PM whether the user actually needs sub-minute freshness. If they don't, the cache can be left as-is and the concern is resolved at the requirements layer."
>
> *Why this is Valid:* It challenges the shared assumption that all six perspectives accepted (that freshness is required), and proposes resolving the concern at the requirements layer rather than the technical layer. The assumption was challenged — and the Innovator is calling for investigation before any code change.

## Checklist

- [ ] Scope is clearly defined before review begins.
- [ ] Advocate has identified at least 3 strengths.
- [ ] Skeptic has examined failure modes and edge cases.
- [ ] Guardian has reviewed auth, validation, secrets, and logging.
- [ ] Craftsperson has checked type safety, patterns, and test quality.
- [ ] User Champion has verified result visibility and accessibility.
- [ ] Synthesizer has resolved conflicts and identified cross-cutting themes.
- [ ] Innovator has presented at least one genuinely alternative framing not raised by any other perspective (or documented the assumption challenged and why it held).
- [ ] All findings include file/line references or concrete examples.
- [ ] Severity ratings are assigned and Guardian findings are not deprioritized.
- [ ] Action items are numbered, assigned to a perspective, and implementable.
