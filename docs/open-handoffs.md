# Open Agent Handoffs

Track cross-agent escalations here. When an agent cannot resolve a decision alone, it escalates to another agent using this log. Entries survive session boundaries — the receiving agent reads this file on their first invocation.

> **Do not record secrets, credential values, environment-specific paths, or PII in this file.**

---

## Format

```
### [OPEN | RESOLVED | DEFERRED] Handoff: [Short description] — [Date]
**From:** [Agent name]
**To:** [Agent name]
**Situation:** [1-2 sentences: what triggered the escalation]
**Background:** [Context the receiving agent needs — what's been tried, what constraints apply]
**Assessment:** [What the escalating agent concluded and why it cannot resolve this alone]
**Decision needed:** [Specific question or decision for the receiving agent]
**Resolution:** [Fill in when status changes to RESOLVED or DEFERRED]
```

---

## Example

```
### [RESOLVED] Handoff: Session storage strategy — 2024-01-15
**From:** Engineer
**To:** System Architect  
**Situation:** Implementing user session persistence. Two approaches viable: database-backed sessions vs. signed JWT with refresh tokens. Each has different security and scalability tradeoffs.
**Background:** Current auth uses JWT for API access. Frontend expects session to persist across browser refreshes. Team has not yet decided on session expiry policy.
**Assessment:** JWT refresh flow is simpler to implement and stateless, but requires client-side token storage which the Security instructions flag as a risk. DB-backed sessions are safer but add a DB query to every authenticated request.
**Decision needed:** Which session strategy should be used as the architectural default?
**Resolution:** Architect chose DB-backed sessions with 24h expiry. Short-lived JWTs (15min) with DB refresh token rotation. Justification: security > simplicity for this domain.
```

---

*Keep OPEN entries to ≤5. If there are more than 5 open entries, escalate to the team — there may be a process breakdown.*
