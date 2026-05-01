---
name: solution-scouting
description: "Deep technical research methodology for finding prior art, benchmarking existing solutions, and translating cross-domain patterns into actionable proposals. Use before committing to a significant architectural or algorithmic decision."
---

# Solution Scouting

## When to Use

- Before major architectural decisions — before committing to a data model, service boundary, or integration pattern, scout what already exists
- When reinventing something that likely exists — if you are about to build a general-purpose mechanism (rate limiting, event sourcing, graph traversal), prior art almost certainly exists
- When "we built it ourselves" may cost more than adopting — total cost of ownership includes maintenance, security patching, and documentation; scout existing solutions before assuming custom is cheaper
- When you need evidence that a proposed approach is used and proven at scale — "it seems like it should work" is not the same as "it works at 100k requests/day in production"
- When evaluating competing libraries, patterns, or frameworks — structured scouting produces a defensible recommendation; gut feel does not
- When the Innovator has proposed an alternative framing and that framing needs validation before the council commits

## Scouting Tiers

Research is not all equally trustworthy. Apply these tiers in order — do not skip Tier 1 to go directly to Tier 3.

### Tier 1 — First-Party Sources
Official documentation, language specifications, maintainer blog posts, official changelogs, and RFC/proposal processes. Highest trust. Read before anything else. If the official docs say a pattern is deprecated, no Tier 3 discussion can override that.

What to look for: intended use, known limitations, migration paths, security advisories, performance characteristics documented by the maintainers themselves.

### Tier 2 — Production Case Studies
Conference talks (Strange Loop, QCon, AWS re:Invent, KubeCon), engineering blogs from known-scale companies (Stripe, Shopify, Cloudflare, Netflix, Discord, Figma engineering blogs), public postmortems, and GitHub issues/discussions from mature, widely-deployed projects. "We ran this in production for two years and here's what happened" is gold.

What to look for: scale context (requests/day, data volume, team size), failure modes encountered, migration stories, architectural regrets, and performance at scale. The regrets section of a postmortem is often more valuable than the success story.

### Tier 3 — Community Synthesis
Stack Overflow accepted answers (check date — stale answers are common), well-upvoted discussions on Reddit and Hacker News, curated newsletters (Changelog, Bytes, Pointer), and podcasts from practitioners (Syntax, The Changelog, Software Engineering Daily). Valuable but derivative — always trace a Tier 3 claim back to a Tier 1 or Tier 2 source before trusting it.

What to watch for: recency (API churn means 3-year-old answers may be wrong), popularity bias ("everyone uses X" without asking if X fits this context), and conflation of different use cases.

### Tier 4 — Adjacent Domains
Academic papers (especially for algorithms, data structures, distributed systems theory, and cryptography), cross-domain patterns from biology, logistics, supply-chain management, military decision theory, and game design. Requires translation effort but yields genuine novel insight that no one in the direct community has considered yet.

What to look for: structural analogies — not surface similarity but the same underlying problem. The goal is to identify the formal structure of the problem and find where that structure has been solved elsewhere. Translation is the critical skill: a directly imported idea from another domain is usually wrong; a translated one can be transformative.

## The 10x Solution Test

For every proposed solution, ask: does a 10x better version of this exist somewhere that we haven't considered?

Not 10% better — a fundamentally different approach that makes the problem trivial or eliminates it entirely. Examples:
- Instead of optimizing the cache hit rate, eliminate the need for a cache by redesigning the data access pattern
- Instead of building a complex permission system, adopt a proven authorization model (RBAC, ABAC, ReBAC) that already solves this at scale
- Instead of writing a custom parser, use a grammar-based parser generator that handles the entire class of problem

For every 10x solution found, answer: why are we not using it? Is the blocker real or assumed?

Document the answer even if the 10x solution is impractical. Knowing why you can't use the best available solution reveals which constraints to focus on relaxing over time. A constraint that blocks a 10x improvement deserves scrutiny that a constraint blocking a 10% improvement does not.

## Confidence Reporting

Every scouting finding must be reported with a calibrated confidence level. Do not present a finding without one.

- 🟢 **High confidence**: multiple independent production case studies at comparable scale + active maintainer confirmation (or recent official docs) + no major unresolved contradictions between sources. Safe to build on without a spike.
- 🟡 **Medium confidence**: used in production by credible sources but limited independent validation; or well-reasoned with strong Tier 1 backing but limited real-world evidence at the required scale; or a solid Tier 2 source that is more than 18 months old. Proceed, but design for the possibility of being wrong.
- 🔴 **Low confidence**: theoretical, experimental, or single-source claim; Tier 3 or Tier 4 only with no Tier 1/2 corroboration; proposed approach has no known production deployments at the required scale. Deserves a spike before commitment.

## Procedure

1. **Define the decision question with scope** — what must be true about the solution? What are the hard requirements (scale, latency, operational complexity budget, licensing constraints)?
2. **Scout Tier 1 sources** for existing official patterns — start with the official docs of any technology already in the stack
3. **Search Tier 2** for production evidence at comparable scale — specifically look for companies with similar constraints
4. **Run the 10x Solution Test** — what would a fundamentally better solution look like, and why is it or isn't it an option?
5. **Scout adjacent domains** for structural analogs — apply the Analogous Domain Search from `#creative-problem-solving`
6. **Synthesize findings into a comparison table** — solution name, source tier, confidence level, key tradeoffs
7. **Produce a ranked recommendation** with confidence levels and explicitly named unresolved questions

## Output Format

For solution scouting, the output is a **Scout Report**:

```
## Scout Report: [Decision Question]

### Decision Question
[What must be true about the solution? Hard requirements.]

### Solutions Evaluated

| Solution | Source Tier | Confidence | Key Tradeoffs |
|----------|-------------|------------|---------------|
| ...      | Tier 1/2/3/4 | 🟢/🟡/🔴  | ...           |

### 10x Solution Scan
[Does a 10x better solution exist? What is it? Why are we or aren't we using it?]

### Adjacent Domain Findings
[Structural analogs from other domains, with translation notes]

### Recommendation
[Ranked recommendation with confidence level and named unresolved questions]

### Recommended Next Step
[ ] Adopt — confidence is sufficient, proceed
[ ] Spike — confidence is medium/low, design a timeboxed experiment first (see #experimental-design)
[ ] Defer — the decision is premature; gather more information first
```

## Anti-Patterns

- **Research theater**: spending a week researching without producing a recommendation. Scouting must end with a Scout Report. If the scout takes more than a day, check whether the decision question is too broad.
- **Recency bias**: preferring the newest solution regardless of maturity. A library with three months of production history is not ready for a core architectural slot. Age is signal, not noise.
- **Popularity bias**: "everyone uses X" without asking whether X is right for this context. npm download counts, GitHub stars, and Stack Overflow mentions reflect what is commonly used, not what is best for your specific constraints.
- **Not translating**: finding an analog from another domain but failing to map it to the actual problem. A cross-domain finding that is not translated into concrete design implications is not actionable — it is a curiosity.
- **Stopping at Tier 3**: using community synthesis as the primary source without verifying against Tier 1 or Tier 2. Tier 3 sources are the starting point for a search, not the ending point.
- **Conflating confidence levels**: presenting a Tier 3-only finding with the same weight as a Tier 1+2 finding. Confidence must be explicit.
