---
name: "Multi-Model Council Review"
description: "Run a council review across multiple AI assistants for genuine perspective independence. Use for High-Stakes security/auth reviews where single-model independence is insufficient."
argument-hint: "Paste the code diff or describe the change to review. This prompt structures three invocations across different models."
user-invocable: true
---

# Multi-Model Council Review

## Why Multi-Model

The standard Review Council is simulated by a single AI model playing seven roles. All seven perspectives share the same training weights, the same biases, and the same blind spots. Structured single-model analysis is more thorough than unstructured single-model analysis — but it is not independent review.

Different AI models have genuinely different training data, RLHF reward signals, and architectural choices. A finding that one model consistently misses may be surfaced by another. For High-Stakes security decisions — auth flows, schema migrations, API contract changes — the cost of a missed finding is high enough that genuine independence is worth 3× the time investment.

This workflow structures three invocations across at least two different AI assistants. The Synthesizer in the third invocation reads all prior output and reconciles it — giving you the benefit of cross-model divergence without losing coherence.

## When to Use

- Security and authentication changes before they ship to production.
- Architectural decisions before large refactors that are hard to reverse.
- Any change where a single-model Guardian finding feels insufficient and you need a second opinion you can trust.
- Regulated environments where independent review is a compliance requirement.

Do **not** use for routine feature reviews, refactors, or documentation changes — Standard Mode is faster and appropriate there.

## Workflow

**Prerequisites:** Access to at least two different AI assistants (e.g., GitHub Copilot + Claude, or Copilot + ChatGPT).

---

### Invocation 1 — Security and Skepticism
*Run in your primary AI assistant. Copy the full output before closing the session.*

**Prompt:** "You are conducting a two-perspective security and risk review of the following change. Write the Guardian perspective first (security, auth, secrets, input validation, data exposure). Then write the Skeptic perspective (fragile assumptions, failure modes, edge cases, scale risks). Do not write any other perspectives. Do not summarize or synthesize — just the two perspectives, each clearly labeled."

[Paste your code diff or change description here]

---

### Invocation 2 — Quality, Usability, and Strengths
*Run in a **different** AI assistant — different model, separate session. Copy the full output before closing.*

**Prompt:** "You are conducting a three-perspective quality review of the following change. Write the Advocate perspective first (what is strong, worth preserving, good practices). Then the Craftsperson perspective (code structure, DRY, type safety, test quality, maintainability). Then the User Champion perspective (can users find results, recover from errors, navigate clearly — accessibility, feedback, empty/error states). Do not write any other perspectives. Do not summarize or synthesize."

[Paste the same code diff or change description here]

---

### Invocation 3 — Synthesis and Innovation
*Run in any AI assistant. Paste the **full output** from both Invocation 1 and Invocation 2 before the prompt.*

**Prompt:** "You are the Synthesizer and Innovator for a multi-model council review. The following output is from two separate AI assistants — the Guardian + Skeptic from one model, and the Advocate + Craftsperson + User Champion from a different model. 

First, run the Synthesizer: compare findings across all five perspectives, identify conflicts and agreements, find cross-cutting themes, and produce a unified assessment of the most important issues.

Then run the Innovator: read all five perspectives and the Synthesizer's synthesis. Present ONE genuinely alternative framing or cross-domain insight that no perspective has raised — not a better implementation of the current approach, but a different approach entirely. If no better alternative is apparent, state which assumption you challenged and why it held.

End with Prioritized Action Items numbered by severity."

[Paste the full output from Invocation 1 here]
[Paste the full output from Invocation 2 here]

---

## Output

The final output of Invocation 3 is your Multi-Model Council Review. Treat the Guardian findings from Invocation 1 with the same weight as findings from the standard council — they cannot be dismissed without an ADR.

If the two models disagree on a finding (Invocation 1 Guardian flags something Invocation 2 Craftsperson accepts), **the more conservative finding takes precedence.** Surface the disagreement explicitly in the final output so the human can make an informed decision.

## Notes

- This takes approximately 3× longer than a Standard Mode review.
- Reserve it for changes where the cost of a missed finding is high.
- The Invocation 3 Synthesizer has the most context — it sees what both prior models found. Its synthesis is the most reliable output.
- A finding that appears in **both** Invocations 1 and 2 from **different perspectives** is a high-confidence finding regardless of model agreement.
