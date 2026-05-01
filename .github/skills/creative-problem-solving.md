---
name: creative-problem-solving
description: "Lateral thinking, problem reframing, constraint removal, and cross-domain pattern recognition. Use when the team is anchored on a solution that may not be optimal or when a problem has been stated in a way that bakes in unnecessary assumptions."
---

# Creative Problem Solving

## When to Use

- When first-solution bias is setting in — the first idea proposed is being treated as the only idea
- When "we need to do X" actually means "we need to achieve Y" — the solution has been baked into the problem statement
- When a design review surfaces "this works but feels wrong" — a signal that the problem framing may be off
- When a constraint has been accepted without being questioned — especially constraints that arrived as organizational habits rather than hard requirements
- When the team has been working on the same problem for a while and forward progress has stalled
- Before a major architectural commitment, to ensure the chosen direction has been pressure-tested against alternatives

## Core Techniques

### Problem Inversion
Instead of asking "how do we do X?", ask "how would we guarantee that X fails?" — then invert the answer. This surfaces hidden assumptions and fragile dependencies that the forward-looking view misses. If you can enumerate every way the current approach fails, you have a map of its weaknesses. The strongest current weaknesses often point to the most important reframes.

### Five Whys Reframing
Ask why the problem matters five times. The fifth answer is usually the real problem. The first answer is usually the symptom. This is not just a root-cause technique for bugs — it applies to feature requests, architectural decisions, and process problems. "We need to cache this response" → "Why?" → "It's slow" → "Why?" → "The query scans the full table" → "Why?" → "There's no index" → "Why?" → "We didn't know the access pattern". The real problem was missing observability about query performance, not a missing cache layer.

### Constraint Removal Thought Experiment
Remove each constraint one at a time and design the ideal solution in that unconstrained world. Then re-examine each constraint: is it real, or was it assumed?

- **Real constraints** are physics (latency is bounded by the speed of light), regulation (GDPR applies regardless of preference), and firm customer commitments (the SLA was signed). These cannot be removed.
- **Assumed constraints** are organizational habits ("we've always done it this way"), comfort zones ("we don't have Go experience"), and legacy decisions that can be revisited ("this was designed for 100 users, not 100,000"). These can be negotiated.

The goal is not to ignore real constraints but to stop treating assumed constraints as if they were real.

### Analogous Domain Search
How is this problem solved in: a different industry? a different programming paradigm? a different scale? a different culture? Pattern-matching across domains yields genuine insight because the same structural problem recurs in many contexts.

Examples:
- Cache invalidation is structurally similar to supply-chain freshness management — the logistics industry has decades of research on it
- Event-driven UI state is structurally similar to a finite state machine in game design
- Circuit breaker patterns in distributed systems come from electrical engineering
- Incident response processes in mature SRE organizations were adapted from military command-and-control theory

The skill is in identifying the structural analogy — not the surface similarity — and then translating the solution back to the original domain.

### SCAMPER Framework
A systematic provocation to transform a solution. Apply each lens to the current approach:

- **Substitute**: what component, step, or assumption could be replaced with something different?
- **Combine**: what two separate things could be merged into one? What if the API and the cache were the same thing?
- **Adapt**: what from another context could be adapted here? What does this resemble in a different domain?
- **Modify / Magnify / Minimize**: what happens if you make it 10x bigger? 10x smaller? What breaks, and what does that reveal?
- **Put to other uses**: what else could this component, data structure, or mechanism do that it currently doesn't?
- **Eliminate**: what can be removed entirely? What is the simplest version of this that still solves the root need?
- **Reverse**: what happens if you flip the direction, the ownership, or the flow? Instead of push, pull. Instead of server-side, client-side.

Not every lens will yield insight for every problem. Apply all of them quickly and pursue only the ones that generate genuine alternatives.

### Pre-Mortem Analysis
Imagine the solution has been deployed and has failed catastrophically six months from now. What happened? Write the failure story in detail — not vague "it didn't scale" but "at 10,000 concurrent users the connection pool exhausted because the migration ran during peak load." This surfaces risks the forward-looking view systematically misses because forward-looking analysis is optimistic by nature.

The pre-mortem is most valuable for: irreversible decisions, high-stakes architectural choices, new technology adoptions, and any plan that depends on multiple things going right simultaneously.

### 10x Thinking
What would a solution look like that is 10x better than the current proposal — not 10% better? This question is designed to force architectural thinking rather than incremental optimization. A 10% improvement is usually achievable by polishing the current approach. A 10x improvement usually requires a different approach entirely.

The 10x question is not an instruction to build the 10x solution. It is a diagnostic: if a 10x solution exists but you are not building it, why? Is the blocker real or assumed? If real, can it be addressed incrementally? This question often reveals which constraints are genuinely limiting and which are just unexplored.

## Procedure

A step-by-step guide for running a creative problem-solving session:

1. **State the problem as currently understood** — write it down verbatim as it was received
2. **Apply Five Whys** — find the root need behind the stated problem
3. **List all assumptions** and mark each as Real (constrained) or Assumed (negotiable)
4. **Apply Constraint Removal** on each Assumed constraint — design the ideal solution without it
5. **Run an Analogous Domain Search** for the root need — what does this problem resemble in other domains?
6. **Use SCAMPER** on the current proposed solution — apply each lens and note any alternatives generated
7. **Run a Pre-Mortem** on the current proposal — what failure modes emerge?
8. **Synthesize 2–3 distinct alternative framings** from the above — each must address the root need and be genuinely different
9. **Select the most promising alternative** and propose a concrete next step: a spike, a comparison, or a direct recommendation

## Anti-Patterns

- **Brainstorming without convergence**: generating alternatives but never selecting one. Creative sessions must end with a concrete recommendation or a defined next step.
- **First-idea anchoring**: presenting the first reframe as the only one considered. The procedure above exists to prevent this — follow it.
- **Creativity theater**: reframing without any concrete change to the approach. If the reframe doesn't change what gets built, it wasn't useful.
- **Ignoring real constraints**: treating physics, regulation, and firm contracts as "assumed." Real constraints are real. The goal is to challenge assumed ones, not to pretend constraints don't exist.
- **Novelty for its own sake**: choosing a creative solution because it's interesting, not because it's better. Every proposed alternative must be better on at least one measurable dimension — not just newer or more elegant.

## Checklist

Before presenting a creative reframe, confirm:

- [ ] Root need identified via Five Whys (not just the stated problem)
- [ ] At least 2 distinct alternative framings considered (not variations of the same idea)
- [ ] All assumptions explicitly classified as Real or Assumed
- [ ] At least one cross-domain analog explored
- [ ] Most promising alternative has a concrete next step (spike, comparison, or direct recommendation)
- [ ] Proposed alternative is genuinely better on at least one measurable dimension — not just novel
- [ ] Pre-mortem applied to the current proposal (failure modes identified)
