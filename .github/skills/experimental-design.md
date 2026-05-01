---
name: experimental-design
description: "Design and execute timeboxed technical spikes and hypothesis tests that convert uncertainty into evidence. Use when a creative or risky proposal needs validation before architectural commitment."
---

# Experimental Design

## When to Use

- When a solution is promising but untested in this context — "it works in theory" needs to become "it works here"
- When a technology decision carries significant risk — adoption cost, migration cost, or operational complexity that would be painful to reverse
- When two approaches have unclear relative performance — benchmarks, memory profiles, or developer-experience comparisons that cannot be resolved by reasoning alone
- When the Innovator or Architect needs evidence before the council commits — a spike is the structured way to produce that evidence
- When "let's just try it and see" comes up in planning — the experimental-design skill gives that instinct a structure that makes the result meaningful
- When a `#solution-scouting` report returns 🔴 Low confidence or 🟡 Medium confidence on a critical path decision

## The Hypothesis Format

Every experiment must begin with a written hypothesis. Without a hypothesis, no result is conclusive — you can only say "something happened," not "the claim was confirmed or refuted."

```
We believe [CLAIM]
will result in [MEASURABLE OUTCOME]
and we will know this is true when [VALIDATION CRITERION].
Time-box: [DURATION].
If the criterion is not met, we will [FALLBACK DECISION].
```

- **CLAIM**: specific and falsifiable. "This approach is fast" is not a claim. "Replacing the ORM query with a raw SQL join reduces p99 latency below 50ms at 1,000 concurrent users" is a claim.
- **MEASURABLE OUTCOME**: a metric, a passing test, a demo, a line-count comparison — not "it works" or "it feels better." If you cannot measure it, you cannot validate it.
- **VALIDATION CRITERION**: binary or numeric. Pass/fail. ≥X requests/second. ≤N lines of implementation code. If the criterion requires judgment to evaluate, make it more specific.
- **TIME-BOX DURATION**: strict. Specify a wall-clock duration (e.g., "4 hours", "2 days") or a specific end date. When the time-box expires, the experiment ends and results are evaluated on current evidence regardless of whether the work feels complete.
- **FALLBACK DECISION**: defined before the experiment starts, not after results are known. "If the criterion is not met, we proceed with the current approach" or "we adopt option B" or "we defer the decision and revisit in sprint N."

The fallback decision is the most commonly omitted part and the most important. Without it, a failed experiment leads to an open-ended discussion. With it, a failed experiment leads to a predetermined action.

## Spike Types

### Technology Spike
**Question**: Does technology X do what we need?
**Structure**: Proof-of-concept that exercises the specific capability under investigation. Not a full integration — the minimal code path that confirms or refutes the capability claim.
**Success criterion example**: "The library can parse a 10,000-node AST in under 100ms on the target runtime."

### Performance Spike
**Question**: Does approach X meet our latency or throughput requirements?
**Structure**: Benchmarked comparison under realistic load. Must include a baseline (current approach) for the result to be meaningful.
**Success criterion example**: "Raw SQL join approach achieves p99 ≤ 50ms at 1,000 concurrent users, compared to ORM baseline of 180ms p99."

### Feasibility Spike
**Question**: Can we implement feature Y within the existing architecture?
**Structure**: End-to-end stub that proves the integration path exists — not a full implementation, but enough to confirm that the components can be connected without architectural surgery.
**Success criterion example**: "A request can flow from the frontend through the new service boundary to the database and return a valid response with no changes to the authentication middleware."

### Risk Spike
**Question**: What are the failure modes of approach Z?
**Structure**: Deliberately break the proposed approach under synthetic load, adversarial input, or failure injection. Document every failure mode observed.
**Success criterion example**: "The new cache layer degrades gracefully under cache-miss storms — response times increase by less than 3x under 100% cache miss rate, and no requests are dropped."

## Spike Hygiene Rules

1. **Spikes are throwaway by default.** Spike code does not go to production unless it has been explicitly reviewed, tested to coverage standards, and approved by the Engineer. The moment spike code is considered for production, it exits the spike lifecycle and enters the normal engineering workflow.

2. **Time-box is inviolable.** When the time-box expires, summarize current evidence and make the fallback decision. Do not extend without writing a new hypothesis with a new validation criterion and a new time-box. "We just need a bit more time" is the most common way spikes become unbounded exploration.

3. **Document in a spike log.** Even an inline comment block counts. Record: the hypothesis, what was actually built or tested, the result against the validation criterion, and the decision made. Future engineers encountering this code or this architectural choice need to know why it was made and what was tested.

4. **Kill switches for production-adjacent spikes.** If a spike does inform production code, it must ship with a feature flag or configuration toggle so it can be disabled without a deploy. This is not optional — it is the price of moving spike work into production.

5. **Test the failure path, not just the success path.** A spike that only tests the happy path has not validated the claim — it has confirmed that the approach works when everything goes right. Test the failure modes. A spike that establishes failure mode behavior is more valuable than one that confirms success under ideal conditions.

## Procedure

1. **State the uncertainty to be resolved** — write it as a question: "Can X do Y under constraint Z?"
2. **Write the hypothesis** in the standard format — claim, measurable outcome, validation criterion, time-box, fallback
3. **Define the minimal experiment** — what is the smallest test that would confirm or refute the hypothesis? Resist the pull toward completeness; a spike that scopes to the exact uncertainty is more useful than one that accidentally becomes an implementation
4. **Set the time-box** — calendar it; when the time expires, stop
5. **Execute** — build only what the hypothesis requires
6. **Document results** against the validation criterion — numbers, not impressions
7. **Make the explicit decision**: adopt / reject / extend with new hypothesis (not "extend with more time")

## Experiment Anti-Patterns

- **Hypothesis-free exploration**: "let's play with X and see what happens." Without a validation criterion, no result is conclusive. Every exploration that is intended to inform a decision must be structured as a hypothesis test.
- **Time-box creep**: extending a spike because "we just need a bit more time." When a spike needs more time, it means the scope was too large or the hypothesis was too broad. Write a new hypothesis for the remaining uncertainty and start a new time-box.
- **Survivorship bias**: declaring success when the experiment worked under ideal conditions. Always test the failure path. A system that works perfectly under happy-path conditions but fails silently under edge conditions has not been validated — it has been given a false positive.
- **Prototype-to-production drift**: spike code that gradually becomes the production implementation without proper review or test coverage. This is one of the most common and costly anti-patterns in engineering. The moment spike code is considered for production, treat it as new feature code: review it, test it, document it.
- **Post-hoc hypothesis**: writing the hypothesis after results are known. This is the experimental equivalent of p-hacking. The hypothesis must be written before the experiment starts, and the fallback decision must be defined before results are seen.

## Output Format

After a spike, produce a **Spike Summary**:

```
## Spike Summary: [Hypothesis Claim]

### Hypothesis
We believe [CLAIM]
will result in [MEASURABLE OUTCOME]
and we will know this is true when [VALIDATION CRITERION].
Time-box: [DURATION].
Fallback: [FALLBACK DECISION].

### Method
[What was actually built or tested — how the hypothesis was exercised]

### Results
[Quantified results against the validation criterion]
Criterion met: YES / NO / PARTIAL

### Decision
[ ] Adopt — criterion met, proceed with this approach
[ ] Reject — criterion not met, execute fallback decision: [FALLBACK]
[ ] Extend — new hypothesis: [NEW HYPOTHESIS] with time-box: [NEW DURATION]

### Spike Artifacts
[Where the throwaway code lives, if anywhere. Note: spike code does not go to production without Engineer review and test coverage.]

### Carry-Forward Risk
[If adopting: what uncertainty remains? What still needs to be proven in the production implementation?]
```
