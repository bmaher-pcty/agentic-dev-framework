---
name: research-methodology
description: "Structured research process for the Researcher agent: question scoping, source discovery, perspective inventory, evidence collection, multi-perspective synthesis, balance audit, and Research Brief delivery. Distinct from #internet-research (which governs evidence gathering mechanics) and #solution-scouting (which focuses on technology options) — this skill governs the full research workflow from commission to brief."
---

# Research Methodology

## When to Use

- When any agent needs a structured process for executing a commissioned research task end-to-end
- When research involves multiple competing perspectives that must all be fairly represented
- When the question requires identifying stakeholders the commissioning agent did not name
- When research findings will be used to inform a council decision or a significant agent output
- When the quality of the research process itself must be auditable (e.g., the commissioning agent needs to know how conclusions were reached, not just what they are)

**Distinct from:**
- `#internet-research` — that skill governs *how* to gather evidence from the web (search mechanics, source credibility, skeptical rules). This skill governs the *full research workflow* that frames what to search for and what to do with what is found.
- `#solution-scouting` — that skill is specific to technology and solution options with the Scout Report format. This skill is domain-agnostic and produces a Research Brief.
- `#evidence-synthesis` — that skill governs *how to combine and present* evidence once gathered. This skill governs the full process including pre-gathering steps. Use both together: this skill structures the process; `#evidence-synthesis` governs the output stage.

## The Research Process

Research is not "search until you find something that answers the question." It is a structured process with defined phases. Skipping phases is the primary source of research failures.

### Phase 1 — Question Scoping

Before searching for any evidence, audit the question.

**Question Audit Checklist:**
- [ ] Is the question stated in a way that could bias the answer? (e.g., "why is X better?" → restate as "what are the tradeoffs of X vs. Y?")
- [ ] Is the question answerable with external evidence, or is it a values question that evidence cannot resolve?
- [ ] Is the scope of the question defined? (What counts as in-scope evidence? What time period? What context?)
- [ ] What decision will this research inform? (Knowing the decision shapes which evidence matters.)
- [ ] What is the "minimum viable research" — the smallest evidence set that would be sufficient for the decision?

If the question fails the audit, restate it before proceeding. Document the restatement and reason in the Research Brief's Commission Details.

**Scope Definition:**
Every research question needs explicit scope boundaries:
- **In scope**: topics, technologies, timeframes, contexts, geographies that are relevant
- **Out of scope**: explicitly named exclusions — prevents scope creep and gives the commissioning agent a chance to correct the scope before work begins
- **Time horizon**: how recent does evidence need to be? (Security standards: <12 months. Technology trends: <24 months. Foundational patterns: 5+ years acceptable if still current)

### Phase 2 — Perspective Inventory

Before collecting evidence, identify every stakeholder perspective relevant to the question. This is the step most likely to be skipped and the one that produces the most valuable insight.

**Systematic Perspective Discovery:**

Start with the obvious perspectives (the ones the commissioning agent named or implied). Then apply the following expansion techniques:

1. **Opposing stakeholders**: who has the most to lose if the evidence points in the direction the question implies? What do they see that proponents miss?
2. **Adjacent stakeholders**: who is affected by this question but not directly involved in the decision? (e.g., researchers studying technology trends vs. practitioners using it)
3. **Temporal perspectives**: what did experts believe about this question 3 years ago? 10 years ago? How have perspectives shifted and why?
4. **Scale perspectives**: does the answer differ for small teams vs. large organizations? Early-stage vs. mature systems? Different industries?
5. **Geographic/regulatory perspectives**: does the answer differ by jurisdiction, compliance context, or cultural context?
6. **Minority views**: what does the credible minority believe that contradicts the mainstream view? Why might the mainstream be wrong?

**Perspective Inventory Output:**
Document every perspective identified before collecting evidence. The brief must include perspectives that were identified but found to have no significant evidence base — "this perspective was considered and found to have no substantial evidence" is a valid finding.

### Phase 3 — Source Discovery

With the question scoped and perspectives inventoried, identify sources using the Source Evaluation Framework (see below). Apply source tiers from `#solution-scouting` as the baseline, extended with research-specific credibility criteria.

**Minimum source requirements:**
- At least 3 independent sources overall
- At least 1 source per major perspective identified in Phase 2 (or documented explanation of why none was found)
- At least 1 Tier 1 or Tier 2 source for any finding rated 🟢 High confidence

**Search strategy documentation:**
Record the search queries and strategies used. This allows the brief to be reproducible and allows the commissioning agent to verify or extend the research.

### Phase 4 — Evidence Collection

Collect evidence against each perspective in the inventory. Apply the Skeptical Evidence Rules from `#internet-research`:
- Start skeptical: single-source claims are unverified
- Upgrade confidence only when multiple independent credible sources converge
- Check publication dates; prefer recent evidence where currency matters
- Note when a source has an affiliation, funding, or commercial interest that could bias its claims

**Evidence collection discipline:**
- Collect evidence that *contradicts* the emerging picture with equal effort as evidence that supports it
- If all collected evidence points in one direction, that is a signal to search harder for the counterargument — not a signal that the question is resolved
- Do not stop collecting evidence when you find an answer; stop when you have represented all perspectives in the inventory

### Phase 5 — Multi-Perspective Synthesis

With evidence collected, apply `#evidence-synthesis` to combine findings across perspectives. Key outputs:
- Where perspectives converge (strong signal)
- Where perspectives diverge (contested territory requiring explicit uncertainty disclosure)
- What the weight of evidence says across all perspectives combined — quality-weighted, not a simple source count

### Phase 6 — Balance Audit

Before finalizing the brief, run the Balance Audit (see below). This is a mandatory quality gate, not an optional checklist.

## Source Evaluation Framework

Sources are not equally credible. Apply this framework to every source before including it in a brief.

### Credibility Dimensions

**1. Authorship**
- Is the author identifiable? What are their credentials in this domain?
- Is the author affiliated with an organization? Does that affiliation create an incentive to present findings in a particular way?
- Is the author a primary source (original research, direct experience) or a secondary source (summarizing, interpreting, or citing others)?

**2. Independence**
- Is this source independent of other sources cited, or is it drawing from the same underlying study, data, or organization?
- Five sources citing the same original study are one independent data point, not five.
- Look for the original source; do not count derivatives as independent corroboration.

**3. Recency**
- When was this published or last updated?
- Is the domain one where knowledge changes rapidly (security, cloud services, ML frameworks: <12 months preferred) or slowly (fundamental algorithms, networking principles: older sources acceptable)?
- If citing an older source, note whether its claims are still current.

**4. Peer Review or Editorial Standards**
- Was this peer-reviewed, editorially reviewed, or self-published?
- Academic papers, established journalism, and official documentation carry editorial accountability; blog posts, social media, and promotional content do not.
- Peer review is not sufficient on its own — publication bias exists. But its absence is a signal to apply additional skepticism.

**5. Evidence Base**
- Is the source's claim based on data, observation, or opinion?
- Data-based claims: what data, how collected, what sample size, what methodology?
- Observation-based claims: what context, how generalizable?
- Opinion-based claims: useful for understanding perspectives, not for establishing facts

### Source Independence Score

Rate each source on a three-point independence scale:
- **Independent**: no affiliation with the organizations, products, or positions being evaluated; no financial stake; no prior published position that would create pressure to reach a specific conclusion
- **Affiliated**: connected to an organization or position with a stake in the finding (e.g., a vendor's documentation about their own product; a study funded by an interested party)
- **Unknown**: authorship or funding is unclear

Affiliated sources are not disqualified — a vendor's official documentation is the authoritative source on their own product. But affiliated sources cannot constitute the primary evidence for a finding. They must be corroborated by independent sources.

### Source Tier Reference (from `#solution-scouting`)

- **Tier 1** — Official documentation, specifications, maintainer guidance, standards bodies. Highest trust.
- **Tier 2** — Production case studies, engineering blogs from known-scale organizations, public postmortems, mature project discussions. High trust where context matches.
- **Tier 3** — Community synthesis (Stack Overflow, Hacker News, Reddit, newsletters). Medium trust — trace claims back to Tier 1/2 before relying on them.
- **Tier 4** — Adjacent domains, academic papers, cross-domain analogs. Variable trust — valuable for structural insights but requires translation.

## The Perspective Inventory

The Perspective Inventory is a structured list of all stakeholder viewpoints relevant to a research question. It is produced in Phase 2 and serves as a completeness check for Phase 4.

**A complete Perspective Inventory includes:**
1. Proponents of the most common or mainstream position
2. Critics of the most common position (not fringe — credible critics)
3. Practitioners (those who have implemented or used what is being researched)
4. Researchers or analysts (those who study the space rather than build in it)
5. End users or downstream affected parties
6. Dissenters or minority schools of thought with credible evidence
7. Adjacent domain experts who approach this question from a different frame

**The inventory rule**: every perspective named in the inventory must either appear in the findings or be explicitly noted as having no substantial evidence base. A perspective that was identified and then silently omitted is an omission bias violation.

## Evidence Weighting

Not all evidence deserves equal weight. The following framework governs how to treat evidence of different types.

### Weight by Source Tier
- Tier 1 (official docs, specifications): highest weight
- Tier 2 (production case studies, engineering blogs from known-scale orgs): high weight
- Tier 3 (community synthesis): medium weight — verify against Tier 1/2
- Tier 4 (adjacent domains, academic papers): variable — valuable for structural analogs, requires translation

### Weight by Convergence
- **Strong signal**: multiple independent Tier 1/2 sources agree, no significant Tier 1/2 contradictions → 🟢 High confidence
- **Moderate signal**: Tier 1/2 sources agree but limited in number, or Tier 3 convergence with some Tier 1/2 support → 🟡 Medium confidence
- **Weak signal**: single source, or Tier 3-only, or significant contradictions unresolved → 🔴 Low confidence

### Handling Contradictory Evidence

When credible sources contradict each other:
1. Do not suppress the contradiction — present both positions
2. Investigate why they contradict: different contexts? Different timeframes? Different definitions of terms?
3. If the contradiction can be explained by context (e.g., "this is true at small scale, not at large scale"), explain this
4. If the contradiction cannot be explained, report it as an unresolved tension with 🔴 Low confidence for any synthesis finding
5. Propose to the commissioning agent what additional research would resolve the contradiction

### Confirmation Bias Check

Before finalizing evidence collection, ask:
- Did I search for evidence that contradicts my emerging finding with the same effort as evidence that supports it?
- If all evidence points one way, have I genuinely looked for dissent, or have I stopped looking after finding confirmation?
- Is the convergence real or is it because I searched using terms that would only return supporting sources?

## The Balance Audit

The Balance Audit is a mandatory quality gate run after Phase 5 and before the brief is delivered. A brief that fails the Balance Audit must be revised before delivery.

### Balance Audit Checklist

**Perspective Completeness**
- [ ] Every perspective in the Perspective Inventory appears in the Findings section
- [ ] Any perspective with no evidence base is documented as such (not silently omitted)
- [ ] The minority or dissenting view appears even if it is outweighed by majority evidence

**Evidence Completeness**
- [ ] At least one source per major perspective was found and cited
- [ ] Contradictory evidence is present and has not been buried or minimized
- [ ] No finding rated 🟢 High confidence rests on fewer than 2 independent sources

**Framing Neutrality**
- [ ] The research question was audited and restated neutrally if needed
- [ ] The brief does not use language that favors one position over another
- [ ] The Pros/Cons analysis gives equal analytical depth to each option
- [ ] The "Recommended Next Steps" section offers options, not directives

**Uncertainty Transparency**
- [ ] Every finding carries a confidence rating
- [ ] Findings with 🔴 Low confidence are explicitly identified as uncertain
- [ ] The "What Remains Unresolved" section names specific open questions

**Source Integrity**
- [ ] Every cited source was actually consulted in this research session
- [ ] Affiliated sources are flagged and corroborated by independent sources
- [ ] Source dates are recorded

**The Impartiality Test**
- [ ] A proponent of the minority view could read this brief and find it fair
- [ ] A proponent of the majority view could read this brief and find it fair
- [ ] The brief does not lead toward a conclusion the evidence does not support

## Anti-Patterns

### Confirmation Bias
Searching for evidence that supports an expected conclusion and stopping when found. Manifests as: all sources agree, no contradictions found, high confidence on everything. The cure: actively search for the counterargument before finalizing.

### Availability Heuristic
Using the most easily found sources rather than the most credible ones. Manifests as: Tier 3 sources dominating because they appeared first in search results. The cure: apply the Source Evaluation Framework before including sources, not after.

### Authority Fallacy
Treating a single prestigious source as sufficient evidence regardless of what other sources say. Manifests as: "Google/AWS/MIT says X, therefore X is true." The cure: apply the independence requirement — even authoritative sources require corroboration on contested questions.

### Selective Citation
Citing sources that support a finding while omitting sources that contradict it. Even when unintentional, this produces a biased brief. The cure: the Balance Audit catches this — every perspective must appear.

### False Balance
Presenting a fringe view as equivalent to an established consensus because "both sides deserve equal representation." Not all disagreements are symmetric. The cure: evidence weighting — report what the evidence actually says about the relative strength of each position.

### Recency Bias
Preferring recent sources regardless of their quality or relevance, while dismissing foundational sources that remain current. The cure: evaluate recency in context — domain maturity determines how much currency matters.

### Scope Drift
Expanding the research question mid-process because interesting adjacent material appears. The cure: document scope boundaries in Phase 1 and explicitly flag when material falls outside scope rather than incorporating it.

### Research Theater
Producing a voluminous brief that signals effort but does not serve the decision. Manifests as: 20 pages of findings when 4 would have served the commissioning agent better. The cure: the "minimum viable research" check in Phase 1 — research is sized to the decision, not to completeness for its own sake.

## Output Format

The output of this skill is a **Research Brief** as specified in the Researcher agent's Output Format section. The brief's Methodology section must document:
- Which phases were executed
- Search strategies used in Phase 3
- Sources consulted per tier
- Balance Audit checklist results
- Any phases that were abbreviated and why (e.g., Phase 2 abbreviated because the commissioning agent defined all required perspectives explicitly)
