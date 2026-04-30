---
name: "Council Review"
description: "Run a multi-perspective review with six specialized reviewers (Advocate, Skeptic, Synthesizer, Guardian, Craftsperson, User Champion) on specific code, files, or features."
argument-hint: "Specify files, features, or a PR to review. Use 'full codebase' for a comprehensive audit."
---

Convene the Review Council to evaluate the specified code or feature from six perspectives.

## Council Members

1. **The  What is strong, well-built, and worth preserving?Advocate** 
2. **The  What assumptions are fragile? What fails at 2 AM?Skeptic** 
3. **The  Where is the best pattern, and where should it be applied everywhere?Synthesizer** 
4. **The  What are the attack vectors, secret risks, and auth gaps?Guardian** 
5. **The  Is this maintainable, testable, DRY, and properly typed?Craftsperson** 
6. **The User  Can users find results, recover from errors, and navigate clearly?Champion** 

## Procedure

1. Identify the review scope from the user's input (files, feature area, or full codebase).
2. Read the relevant code, tests, and configuration.
3. Run each perspective analysis in the order above.
4. Synthesize findings into a unified assessment.
6. Produce prioritized action items with specific file references and recommended fixes.5. Classify each finding by severity (

## Output

1. Council Summary (one paragraph).
2. Advocate Highlights (top strengths with file references).
3. Critical Findings table (Severity | Perspective | Finding | File | Fix).
4. Security Assessment section.
5. UX Assessment section.
6. Prioritized Action Items (numbered, with severity and owner).

## Rules

- Every finding must reference a specific file or line.
- Advocate speaks first; no review opens with criticism.
- Guardian findings cannot be deprioritized.
- Do not recommend weakening tests or security measures.
- Reference `.github/skills/council-review.md` for methodology details.
- Apply the path-scoped security and testing instructions in `.github/instructions/` when evaluating findings.
