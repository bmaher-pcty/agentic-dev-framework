---
name: evidence-synthesis
description: "Combining evidence from disparate sources into a coherent, calibrated, impartial presentation. Governs the output stage of research: clustering claims, identifying convergence and divergence, presenting tradeoffs with equal analytical depth, and communicating uncertainty without false confidence. Distinct from #internet-research (gathering) and #research-methodology (full process) — this skill is invoked at the output stage, after evidence has been gathered."
---

# Evidence Synthesis

## When to Use

- After evidence has been gathered and the task is to combine it into a coherent, balanced presentation
- When findings from multiple sources, perspectives, or timeframes must be integrated without favoring any single source
- When a decision-maker needs to understand what the evidence says across positions, not just what one source says
- When findings include contradictions that must be named and handled rather than resolved by picking a side
- When the output must be consumable by an agent or human who did not participate in the research

**Distinct from:**
- `#internet-research` — that skill governs *gathering* evidence (search mechanics, source credibility). This skill governs *presenting* what was gathered.
- `#research-methodology` — that skill governs the *full research process* including pre-gathering phases. This skill is the output stage — invoke it after Phase 4 (Evidence Collection) is complete.
- `#solution-scouting` — that skill produces a Scout Report oriented toward recommending a solution. This skill produces balanced findings without a recommendation — the decision belongs to the consumer.

**This skill is available to all agents**, not only the Researcher. The Architect combining findings from multiple internal documents, the QA agent synthesizing test failure patterns, and the Guardian summarizing a threat landscape can all invoke this skill without running a full research process.

## The Synthesis Process

### Step 1 — Claim Clustering

Before writing a single sentence of findings, organize the raw evidence into clusters of related claims. A claim is a specific assertion made by one or more sources.

**Clustering method:**
1. List every distinct claim from every source consulted
2. Group claims that address the same question or assert the same type of fact
3. Within each cluster, note which sources agree and which disagree
4. Identify the cluster's "claim center" — the version of the claim that most sources converge on, if any

The cluster structure becomes the organizational spine of the Findings section. Each cluster maps to a finding.

**Why this comes first:** Without clustering, synthesis becomes a source-by-source walkthrough ("Source A says X, Source B says Y, Source C says Z") rather than a topic-organized analysis. Source-by-source presentation forces the reader to do the synthesis themselves. Claim clustering does it for them.

### Step 2 — Convergence and Divergence Mapping

For each claim cluster, classify the evidence pattern:

**Convergent claims**: multiple independent sources agree and no significant credible source contradicts
- Present as an established finding with confidence proportional to source quality and independence
- Do not artificially introduce doubt about well-supported claims
- Note when "convergence" is actually multiple sources citing a single original study — that is concentration, not convergence

**Divergent claims**: credible sources disagree
- Present both (or all) positions with the evidence supporting each
- Investigate the source of divergence: different contexts? Different timeframes? Different definitions? Different scales of application?
- Classify the divergence type:
  - **Contextual divergence**: both positions are correct in different contexts (explain the contexts)
  - **Temporal divergence**: positions reflect different eras (explain when the shift occurred and why)
  - **Scale divergence**: positions reflect different scales of application (explain the threshold)
  - **Irresolvable divergence**: credible experts genuinely disagree with no clear contextual explanation (report as contested, 🔴 Low confidence)

**Isolated claims**: single-source or single-perspective claims with no corroboration
- Present as isolated with explicit low confidence
- Do not suppress — isolated claims may represent an important emerging view or a minority perspective with valid evidence
- Label clearly: "This finding rests on a single source and has not been independently corroborated"

### Step 3 — Handling Contradictions

Contradictions are not a research failure — they are findings. The appropriate response to contradictory evidence is to present the contradiction honestly, investigate its source, and report what resolution is possible.

**Contradiction handling protocol:**
1. Confirm the contradiction is real — not a terminology difference or a context mismatch
2. Investigate the source of the contradiction (see divergence types above)
3. If the contradiction is resolvable by context: explain the context boundary and present both positions as conditionally correct
4. If the contradiction is not resolvable: name both positions, present the evidence for each, assign 🔴 Low confidence to any synthesis finding, and add the question to "What Remains Unresolved"
5. Never resolve a contradiction by choosing the side you find more compelling — that is advocacy, not synthesis

**What not to do with contradictions:**
- Do not suppress the minority position because the majority position has more sources
- Do not present contradictions as resolved when they are not
- Do not assign a "winner" to a contested question — assign confidence levels and defer to the decision-maker

## The Pros/Cons Framework

When research involves comparing options, approaches, or positions, the Pros/Cons analysis must give every option equal analytical depth.

**Equal analytical depth rule:** equal analytical depth means equal rigor of analysis per option, not equal word count. A weak option may have fewer pros than a strong one — that is fine. But the analysis of each option must apply the same scrutiny, the same search for evidence, and the same willingness to surface weaknesses.

### Structure Per Option

For each option identified in the research:

```
### Option: [Name]

**What it is**: [One-sentence description — neutral framing]

**Evidence base**: [Sources, tiers, confidence]

**Strengths (with evidence)**
- [Strength 1]: [Evidence — source, context, confidence]
- [Strength 2]: ...

**Weaknesses (with evidence)**
- [Weakness 1]: [Evidence — source, context, confidence]
- [Weakness 2]: ...

**Context sensitivity**: [Under what conditions is this option stronger or weaker?]

**Who uses it and at what scale**: [Real-world adoption context]

**What proponents say**: [Steelman of the best case for this option]

**What critics say**: [Steelman of the best case against this option]

**Overall confidence in this option's characterization**: 🟢/🟡/🔴
```

**The steelman requirement**: every option must be represented at its strongest, not at a strawman version. If the brief's characterization of an option's proponents is one that those proponents would reject, the characterization is unfair. The Impartiality Test (see below) catches this.

### When All Options Have Weaknesses

The goal is not to find a perfect option — it is to honestly represent the tradeoffs. Findings like "all evaluated options have significant weaknesses in context X" are valid and useful. Do not manufacture advantages for weaker options to create false symmetry.

## Confidence Calibration

Confidence is assigned at the **finding level**, not at the document level. A Research Brief can contain findings at multiple confidence levels. The overall confidence of a brief is not a meaningful summary — what matters is the confidence of each specific claim.

### Confidence Levels

- 🟢 **High confidence**: claim is supported by multiple independent Tier 1 or Tier 2 sources with no significant credible contradiction. Safe to act on without additional verification.

- 🟡 **Medium confidence**: claim is supported by credible sources but either: the sources are not all independent (some trace to the same original), the sources are primarily Tier 3 with limited Tier 1/2 corroboration, the evidence is older than the domain's freshness threshold, or credible but isolated contradictions exist. Proceed with the claim, but design for the possibility of being wrong.

- 🔴 **Low confidence**: claim rests on a single source, unverified Tier 3 sources, or irresolvable contradictions. Treat as a hypothesis, not a finding. Flag for additional research before acting.

### Calibration Rules

- Do not round up confidence to make a brief look more decisive. A 🟡 finding presented as 🟢 is an integrity failure.
- Do not round down confidence to appear appropriately humble. Artificially hedged findings waste the decision-maker's time.
- When assigning confidence, ask: "If I acted on this finding and I was wrong, what would have led me astray?" If the answer is "very little — sources are strong and convergent," that is 🟢. If the answer is "I only found one good source and couldn't find corroboration," that is 🔴.
- Confidence applies to the finding, not the source. A highly credible source making an unverified claim about a new area produces a 🟡 or 🔴 finding, not a 🟢.

### Calibration Consistency Rubric

Use this rubric to self-check confidence assignments before finalizing:

| Signal | Confidence Impact |
|--------|------------------|
| 3+ independent Tier 1/2 sources agree | Strong toward 🟢 |
| Single major Tier 1 source (spec, official docs) with no credible contradiction | Strong toward 🟢 |
| Only Tier 3 sources consulted | Cap at 🟡; requires Tier 1/2 corroboration for 🟢 |
| Single source only, regardless of quality | 🔴 |
| Credible unresolved contradiction present | Cap at 🟡; 🔴 if contradiction is from a high-quality source |
| Evidence > 2 years old in a fast-moving domain | Reduce one level |
| Evidence > 5 years old in any domain | 🔴 unless explicitly confirmed still current |

## The Impartiality Test

Before finalizing a Research Brief, apply the Impartiality Test: a structured self-check that the synthesis is genuinely fair.

### The Four Checks

**1. The proponent test**
If someone who strongly believes in the majority position reads this finding, will they find it accurately represents their view? Would they say "yes, that is what the evidence says"?

**2. The critic test**
If someone who strongly believes in the minority or dissenting position reads this finding, will they find their view fairly represented? Would they say "I disagree with the conclusion, but I cannot say my position was misrepresented or omitted"?

**3. The neutral test**
If someone with no prior position on this question reads this brief, will they understand what is established, what is contested, and what remains unknown — without the brief guiding them toward a conclusion?

**4. The omission test**
Is there any significant perspective, evidence, or counterargument that a fair-minded analyst would expect to see that does not appear in this brief? If yes, add it.

### Failure Modes the Impartiality Test Catches

- Conclusions embedded in neutral-sounding language ("unsurprisingly, X outperforms Y" vs. "X outperforms Y in these specific benchmarks under these conditions")
- Longer or more detailed analysis of the preferred option
- Counterarguments presented as afterthoughts rather than as genuinely competing evidence
- Sources for the preferred position presented first, structurally signaling priority
- Minority views characterized at their weakest rather than their strongest (strawman instead of steelman)

### When the Impartiality Test Fails

If self-assessment reveals a bias in the synthesis, revise the affected sections and re-run the test. Do not deliver a brief that fails the impartiality test. If the evidence genuinely concentrates heavily on one side, say so explicitly: "The evidence strongly supports position X; position Y has limited independent corroboration. This is a finding about the evidence base, not an endorsement of position X."

## Presenting Uncertainty

Uncertainty is information. The instinct to resolve uncertainty before presenting findings produces false confidence. The correct approach is to present findings with their uncertainty intact and label it clearly.

### Uncertainty Vocabulary

Use these precise formulations to communicate degrees of certainty:

| Phrase | When to Use |
|--------|-------------|
| "The evidence establishes..." | 🟢 High confidence — multiple independent sources, no credible contradictions |
| "The evidence suggests..." | 🟡 Medium confidence — reasonable but incomplete support |
| "Some evidence indicates..." | 🔴 Low confidence — single-source or weakly corroborated |
| "It is contested whether..." | Irresolvably divergent findings — name the parties to the contest |
| "No reliable evidence was found on..." | Question was searched; evidence was genuinely absent |
| "This question was outside the scope of this research..." | Question identified but not pursued per scope boundaries |

**Do not use:**
- "Clearly..." — overstates certainty; if it were clear, no research would be needed
- "Obviously..." — same problem, and condescending to the decision-maker
- "It is well-known that..." — treats assertion as evidence; cite who knows it and where
- "Most experts believe..." — vague attribution; name the experts or cite the survey
- "Research shows..." — cite the specific research, not an unnamed body of research

### The Unknown-Unknown Problem

Some gaps in a Research Brief reflect the limits of available evidence (known unknowns). Others reflect questions that were not asked (unknown unknowns). The Perspective Inventory in `#research-methodology` converts as many unknown unknowns as possible into known unknowns — but acknowledging that the inventory itself may have gaps is part of honest reporting.

The "What Remains Unresolved" section of a Research Brief should include:
1. Questions the research tried to answer but couldn't (known unknowns with identified sources of uncertainty)
2. Questions the research identified as relevant but did not pursue (out-of-scope known unknowns)
3. A standing acknowledgment: "There may be perspectives relevant to this question that this research did not identify."

## Anti-Patterns

### The Wall of Text
Synthesis presented as narrative prose that buries key findings in paragraphs. Manifests as: a brief that requires full reading to understand what it says. The cure: claim clustering produces structured findings; use the Research Brief's defined sections with clear headers and labeled confidence levels.

### The Executive Summary That Decides
A summary so strongly front-loaded with a conclusion that the nuanced findings below become irrelevant. Manifests as: the commissioning agent reads only the summary and acts on a conclusion the full evidence doesn't cleanly support. The cure: the summary presents the shape of the evidence — what converges, what is contested, what remains unknown — not a winner.

### Laundered Advocacy
Neutral-sounding language that structurally favors a conclusion. Manifests as: "both options are viable, but Option A addresses the core constraints more directly." The cure: the Impartiality Test catches evaluative language dressed as neutral synthesis. If you would not say the equivalent sentence in favor of the other option, the sentence is not neutral.

### Confidence Homogenization
Assigning the same confidence level to all findings to avoid the effort of differentiating them. Manifests as: every finding is 🟡 Medium, or every finding is 🟢 High. The cure: apply the calibration rubric to every finding independently.

### The False Resolution
Presenting a contested question as resolved by synthesizing the two positions into a middle ground that neither side actually holds. Manifests as: "the evidence suggests a balanced approach combining elements of both." Sometimes this is valid synthesis; often it is an attempt to avoid the discomfort of reporting genuine disagreement. The cure: if the evidence is genuinely contested, report it as contested. Do not manufacture consensus that doesn't exist.

### Completeness Theater
Listing every source found regardless of relevance or quality to signal thoroughness. A brief with 30 marginally relevant sources is harder to consume than one with 8 well-evaluated sources. The cure: include sources that actually contributed to a finding; exclude sources that were consulted and found insufficient (but note they were consulted if their absence is surprising).

### Recency as Authority
Treating the newest source as the most credible regardless of its quality. Manifests as: a recent blog post overriding a foundational specification. The cure: weight by source tier and independence, not by date alone. Currency matters for fast-moving domains; it does not override Tier 1 authority in stable ones.

### Strawmanning the Minority
Characterizing the dissenting position at its weakest to make the majority position look stronger by comparison. This is a subtle and often unintentional form of omission bias. The cure: the steelman requirement in the Pros/Cons Framework — every position is characterized at its strongest before being evaluated.
