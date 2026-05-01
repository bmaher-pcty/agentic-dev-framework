---
name: "Research Request"
description: "Commission the Researcher agent to produce a Research Brief on any question. Use when an agent or user needs evidence-based, multi-perspective findings before making a decision. Produces a structured Research Brief with source inventory, perspective analysis, confidence-rated findings, and recommended next steps for the commissioning agent."
argument-hint: "Complete the intake form. The quality of the Research Brief is directly proportional to the quality of the commission — a vague question produces a vague brief."
---

# Research Request

Commission the Researcher agent to produce a Research Brief. Complete the intake form below. The Researcher will audit the question, identify missing perspectives, execute the research using `#research-methodology` and `#evidence-synthesis`, and return a structured brief — without advocating for a conclusion.

The Researcher **never tells you what to decide.** The Researcher tells you what the evidence says, how strong it is, and what your options are.

---

## Research Commission Intake Form

Answer every field. Fields marked **required** cannot be skipped without reducing research quality. Fields marked *optional* help the Researcher scope more precisely.

---

### 1. The Research Question _(required)_

State the question you need answered. Write it as a genuine question — not as a task description ("research why X is better") or a conclusion disguised as a question ("confirm that X is correct").

**Good question forms:**
- "What are the documented tradeoffs between approach X and approach Y for use case Z?"
- "What does the evidence say about how teams at scale handle problem P?"
- "What perspectives exist on whether standard S is still current best practice, and what alternatives have emerged?"
- "What are the known failure modes of pattern Q in production environments?"

**Framings the Researcher will restate (and why):**
- "Why is X better than Y?" → implies a conclusion; restated as "what are the tradeoffs of X vs. Y?"
- "Research X" → not a question; has no defined answer; Researcher will ask for clarification
- "Confirm that X is correct" → asks for validation, not research; Researcher will reframe as an open question
- "Find evidence that X is the right choice" → this is advocacy, not research; Researcher will disclose the framing issue and restate neutrally

**Your research question:**
> [Write your question here]

---

### 2. The Decision This Research Informs _(required)_

What will you do differently based on this research? Name the specific decision, recommendation, or output this brief will feed into.

The Researcher uses this to determine which evidence is relevant and what "minimum viable research" looks like. Research sized to a decision is useful; research sized to a topic in general is a textbook. A clear decision statement is the single most effective way to get a focused, actionable brief.

**Examples:**
- "The System Architect will choose between approach A and approach B before publishing the ADR for this component."
- "The Review Council needs this to resolve a disagreement between the Skeptic and Craftsperson before issuing a finding."
- "The Thoughtful PM will use this to define acceptance criteria for feature X."
- "The Guardian needs this threat landscape survey before assessing the severity of a security finding."

**Your decision statement:**
> [Name the specific decision or output this brief will feed]

---

### 3. Required Perspectives _(required — list at least one; the Researcher will expand this list)_

Name the stakeholder perspectives that must appear in the brief. The Researcher will systematically expand this list using the Perspective Inventory process from `#research-methodology`, but your named perspectives serve as anchors that cannot be omitted.

You do not need to be exhaustive — the Researcher's job is to identify perspectives you haven't thought of. But name the perspectives most critical to your decision.

**Example perspective types:** practitioners at scale, independent researchers/analysts, early adopters, skeptics of the mainstream approach, organizations with different scale or industry profiles, security-focused reviewers, standards bodies, end users, adjacent communities with relevant experience.

**Required perspectives for your question:**
> - [Perspective 1]
> - [Perspective 2]
> - [add more as needed]

---

### 4. Scope Boundaries _(required)_

Define what is **in scope** and what is **out of scope** for this research. This is not bureaucratic — it is the primary mechanism for getting a brief that is actually sized to your decision rather than a comprehensive literature review.

**In scope:**
> [Topics, technologies, timeframes, contexts, geographies, or comparison dimensions that are relevant to this decision]

**Out of scope:**
> [Explicit exclusions — what you already know well enough, what is adjacent but irrelevant to this specific decision, questions that would expand research without improving the decision you need to make]

---

### 5. Recency Requirement _(optional — defaults to "Standard: < 24 months")_

How current does the evidence need to be? If not specified, the Researcher defaults to Standard.

| Level | Window | Best For |
|-------|--------|----------|
| **Strict** | < 12 months | Security standards, active library ecosystems, vendor pricing, regulatory requirements |
| **Standard** | < 24 months | Technology trends, architectural patterns, tooling comparisons |
| **Relaxed** | 5+ years acceptable | Foundational principles, stable algorithms, long-standing industry practices |

**Your recency requirement:**
> [Strict / Standard / Relaxed — or describe specifically if none of these fit]

---

### 6. Output Format Preference _(optional — defaults to Full Research Brief)_

| Format | Contents | Best For |
|--------|----------|----------|
| **Full Research Brief** | All sections, complete source inventory, full Pros/Cons analysis, Balance Audit results | High-stakes decisions; council deliberation inputs |
| **Focused Brief** | Question, key findings per perspective, confidence levels, recommended next steps; abbreviated source inventory | Lower-stakes decisions; time-constrained contexts |
| **Landscape Summary** | Proactive brief format — missing perspective identified, key evidence, confidence, recommendation on whether full research is warranted | Quick reads before deciding whether to invest in full research |

**Your format preference:**
> [Full Research Brief / Focused Brief / Landscape Summary]

---

### 7. Commissioning Agent and Context _(optional but strongly recommended)_

Who is commissioning this research and why? Knowing the commissioning agent's domain helps the Researcher calibrate depth, select the most relevant source tiers, and frame recommended next steps appropriately for that agent's role.

**Examples:**
- "Review Council — Synthesizer needs external evidence to resolve a disagreement between Skeptic and Craftsperson on caching strategy"
- "System Architect — pre-ADR landscape survey on event streaming options for a system processing ~50k events/day"
- "Thoughtful PM — user research and competitive evidence before writing acceptance criteria for feature X"
- "Guardian — threat landscape on OAuth token storage patterns before assessing severity of an auth finding"
- "Innovator — validating whether an alternative architectural framing has production precedent before presenting it to council"

**Commissioning agent and context:**
> [Agent name and brief context, including any relevant scale, constraints, or decision urgency]

---

## How to Invoke the Researcher

Attach the completed intake form to your Researcher agent invocation as input. The Researcher will:

1. **Audit the question** — restate it neutrally if the framing is biased or leading; explain the restatement if made
2. **Confirm scope** — if scope boundaries are absent or critically ambiguous, the Researcher will ask one clarifying question before proceeding rather than guessing
3. **Execute Phase 1–6 of `#research-methodology`** — question scoping, perspective inventory, source discovery, evidence collection, multi-perspective synthesis, balance audit
4. **Apply `#evidence-synthesis`** — claim clustering, convergence/divergence mapping, Pros/Cons analysis with steelmanning, confidence calibration per finding
5. **Run the Balance Audit** — mandatory quality gate; brief is not delivered if the audit fails until revisions are made
6. **Deliver the Research Brief** — structured, confidence-rated, with the Balance Audit checklist included and recommended next steps framed as options

**Tip:** A well-scoped commission takes less back-and-forth and produces a more useful brief. The most common cause of a vague brief is a vague decision statement (field 2). If you are unsure what the decision is, resolve that before commissioning research — research scoped to "learn about X" rather than "inform decision Y" tends to produce comprehensive briefs that are hard to act on.

---

## What to Expect Back

A completed Research Brief contains these sections in this order:

| Section | What It Contains |
|---------|-----------------|
| **Commission Details** | Your question (and any neutral restatement), the decision it informs, scope, time horizon |
| **Methodology** | Phases executed, search strategies used, source tiers consulted |
| **Source Inventory** | All sources with tier, date, independence rating, and credibility notes |
| **Perspective Inventory** | All stakeholder perspectives identified — including ones you did not name |
| **Findings by Perspective** | What the evidence shows from each perspective, with per-finding confidence level |
| **Pros / Cons Analysis** | Equal-depth analysis of each option with steelmanned proponent and critic positions |
| **Evidence Synthesis** | Where evidence converges, where it diverges, what remains contested |
| **Confidence Assessment** | Per-finding confidence table (🟢/🟡/🔴) with rationale |
| **Balance Audit** | Completed checklist confirming fairness and completeness |
| **What Remains Unresolved** | Explicitly named open questions this research cannot answer |
| **Recommended Next Steps** | Options for the commissioning agent — framed neutrally, never as directives |

**A Research Brief does not tell you what to decide.** It tells you what the evidence says, how strong it is, and what your options look like given that evidence.

---

## How to Consume a Research Brief

### For an Individual Agent

1. **Read Commission Details first** — confirm the brief answers the question you actually asked. If the Researcher restated your question, understand why before using the findings; the restatement may reveal a framing issue worth correcting before acting.
2. **Scan the Confidence Assessment table** — identify which findings are 🟢, 🟡, and 🔴. Weight your decision accordingly. Do not act on 🔴 findings with the same confidence you would act on 🟢 findings.
3. **Read Findings by Perspective** — start with the perspectives most directly relevant to your domain, then read the others. The perspectives you didn't ask for are often where the most valuable information lives.
4. **Check What Remains Unresolved** — are any of the named open questions blockers for your decision? If yes, you have three options: commission a follow-up brief, design a spike (→ Innovator + `#experimental-design`), or explicitly accept the decision under acknowledged uncertainty.
5. **Act on Recommended Next Steps** — these are framed as options, not directives. Choose the option that fits your constraints and role.

### For the Review Council

When a Research Brief is introduced into a council session:

1. **The presenting agent** (usually whoever commissioned the research) summarizes the brief in 2–3 sentences: what was researched, what the key convergence finding is, and what remains contested.
2. **Each council perspective** evaluates the brief through their lens:
   - **Guardian**: were the sources credible? Is the confidence calibration appropriate? Are any security-relevant findings present that require a Guardian assessment?
   - **Skeptic**: what does the "What Remains Unresolved" section reveal about the risks of acting on this evidence? Which 🔴 findings could become production problems if treated as certain?
   - **Craftsperson**: is the brief structured consistently? Are findings actionable, or do they require further scoping before the Engineer can act?
   - **Advocate**: what does the brief confirm about current approach strengths? Does convergent evidence validate existing decisions?
   - **User Champion**: does the brief surface any user, accessibility, or experience implications that the commission didn't explicitly ask about?
   - **Synthesizer**: does the brief change the weight of any existing council finding? Does it resolve a contested position between perspectives?
   - **Innovator**: does the brief reveal a cross-domain pattern, prior art, or assumption the council has not yet challenged?
3. **The Researcher does not vote.** If the Researcher is present during deliberation, they answer questions about methodology or source evaluation. They do not issue findings, severity ratings, or action items — those belong to the seven council perspectives.
4. **Council findings that reference the brief** should cite specific sections: "per the Research Brief's Perspective Inventory, [perspective] was identified but not previously considered in this review."

### Signs of a Well-Used Research Brief

- Decisions reference specific findings and their confidence levels, not just "the research showed X"
- The council distinguishes between 🟢 findings (can act on directly) and 🔴 findings (require a spike or additional evidence before acting)
- The "What Remains Unresolved" section generates explicit follow-up commissions, spikes, or acknowledged-risk statements in the final output
- Perspectives identified by the Researcher but not originally named by the commissioning agent are considered, even if ultimately weighted less heavily

### Signs of a Poorly Used Research Brief

- Only the summary is read; confidence levels are ignored
- 🔴 Low confidence findings are acted on with the same certainty as 🟢 findings
- The "What Remains Unresolved" section is skipped entirely
- The brief is cited to validate a decision already made rather than to inform one being considered
- The Perspective Inventory additions (perspectives the Researcher added beyond what was requested) are not reviewed

---

## Reference

| Resource | Purpose |
|----------|---------|
| `.github/agents/researcher.agent.md` | Researcher agent definition, Ethics Charter, Research Brief format |
| `.github/skills/research-methodology.md` | Full research process — Phase 1 through Phase 6 and Balance Audit |
| `.github/skills/evidence-synthesis.md` | Claim clustering, Pros/Cons Framework, confidence calibration, Impartiality Test |
| `.github/skills/solution-scouting.md` | Source tiers (T1–T4) and Scout Report format for technology-specific research |
| `.github/skills/internet-research.md` | Evidence gathering mechanics, skeptical evidence rules, convincing threshold |
| `.github/skills/experimental-design.md` | Spike design when research identifies a question requiring empirical validation |
