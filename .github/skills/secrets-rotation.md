---
name: secrets-rotation
description: 'Zero-downtime secrets rotation procedure including rotation triggers, dual-write phase, verification, and emergency rotation.'
---

# Secrets Rotation

## When to Use
- Scheduled rotation cadence is reached (see Rotation Triggers below).
- A secret is suspected of exposure — in logs, commits, error messages, or a third-party breach.
- An employee with access to secrets departs (offboarding rotation).
- A secret was accidentally committed to source control or emitted in a log line.
- A third-party service notifies you of a credential compromise.

## Procedure

### 1. Audit Which Services Use the Secret
Before rotating, map every consumer:
1. Search your codebase and deployment configs for references to the secret name.
2. Review the secret manager's access log — which services fetched this secret in the last 30 days?
3. Check CI/CD pipelines that may inject the secret as an environment variable.
4. Document the consumer list before proceeding — you cannot verify rotation is complete without it.

### 2. Provision New Secret Alongside the Old (Dual-Write Phase)
1. Generate a new credential with the same permissions as the old one.
2. Store the new credential in your secret manager under a versioned key (e.g., `API_KEY_v2`).
3. Do NOT revoke the old credential yet. Both old and new must be valid simultaneously during this phase.
4. Confirm the new credential is accessible to services that will consume it.

### 3. Update Dependent Services One by One
1. Update each service to consume the new secret version.
2. Deploy and verify each service individually — do not batch-update all services at once.
3. After each service is updated and verified, move to the next.
4. Keep the old secret active until every consumer is confirmed migrated.

### 4. Verify All Services Use the New Secret
1. Scan application logs for references to the old secret value being used (if observable).
2. Check secret manager access logs — the old secret version should show zero reads from live services.
3. Run the full smoke test suite against each service.

### 5. Revoke the Old Secret
1. Only revoke after all consumers are confirmed on the new secret.
2. Revoke in your identity provider or secret manager — do not simply delete the entry; revoke access.
3. Monitor error rates for 10 minutes after revocation.
4. Document the revocation timestamp.

---

## Zero-Downtime Rotation Protocol (4-Phase Approach)

| Phase | Action | Done When |
|-------|--------|-----------|
| **1. Provision** | Generate new credential; store alongside old | New credential active in secret manager |
| **2. Deploy new consumers** | Update each service to read new credential version | All services deployed with new secret reference |
| **3. Verify** | Smoke tests pass; old secret shows no live reads | Zero consumers using old secret |
| **4. Revoke old** | Revoke old credential in identity provider | Old credential no longer accepted |

Never skip from Phase 1 to Phase 4 — revoking before all consumers are updated causes outages.

---

## Rotation Triggers

### Scheduled Rotation
| Secret Type | Rotation Interval |
|-------------|------------------|
| External API keys | 90 days |
| Database credentials | 90 days |
| Service-to-service tokens | 180 days |
| Internal TLS certificates | 365 days (or expiry - 30 days, whichever is sooner) |
| OAuth client secrets | 180 days |

### Incident-Driven Rotation (Immediate)
Rotate immediately when:
- A secret appears in any log, error message, or error report.
- A secret is found in a git commit (even a private repo — assume exposed).
- A third-party reports a breach of their systems where your credential was stored.
- A service account or API key is used from an unexpected IP or region.

### Departure-Driven Rotation
- Employee with access to secrets departs: rotate all secrets they had access to within 24 hours of access revocation.
- Contractor engagement ends: rotate all secrets used during the engagement on the final day.

---

## Rotation Verification

After completing rotation:

1. **Test new secret works:** Make an authenticated API call or database connection using the new credential from a test environment. Confirm 200/success response.
2. **Test old secret is truly revoked:** Attempt authentication with the old credential. Confirm it is rejected (401/403, not 200).
3. **Confirm no service uses old secret:** Review secret manager access logs 30 minutes after revocation. If any service is still reading the old secret version, it was missed in the migration.
4. **Log scan:** Search application logs for the first 30 minutes post-revocation for authentication errors. Any new auth failures indicate a missed consumer.
5. **Document results:** Record the date, secret name (not value), which services were updated, and verification outcome.

---

## Emergency Rotation (Breach Response)

When a secret is actively compromised:

1. **Revoke immediately** — do not wait for consumers to be updated. An active breach outweighs the risk of a brief outage.
2. Provision a new credential immediately after revocation.
3. Update consumers as quickly as possible — prioritize by blast radius (external-facing services first).
4. File an incident report within 1 hour, even if the rotation is complete.
5. Conduct a post-incident review to identify how the secret was exposed.

> ⚠️ Emergency rotation accepts a brief service degradation as a deliberate tradeoff to stop active credential misuse. This is the only acceptable case to revoke before all consumers are updated.

---

## Checklist

### Preparation
- [ ] All consumers of the secret identified and documented.
- [ ] New credential provisioned in secret manager.
- [ ] Old credential still active (dual-write phase started).

### Rotation Execution
- [ ] Each consuming service updated to new secret version one by one.
- [ ] Each service verified (smoke test) after update before moving to the next.
- [ ] Old secret confirmed to have zero live reads before revocation.

### Verification
- [ ] New secret tested and confirmed working.
- [ ] Old secret tested and confirmed revoked/rejected.
- [ ] Secret manager access logs show no reads of old secret for ≥30 minutes post-revocation.
- [ ] Application error rates stable after revocation.

### Documentation
- [ ] Rotation date and affected secret name (not value) recorded.
- [ ] Incident report filed if rotation was breach-driven.
- [ ] Next scheduled rotation date set.

---

## Anti-Patterns

- **Revoking before all consumers are updated.** This causes an immediate service outage for any consumer still using the old secret. Always complete the dual-write phase before revoking.
- **Rotating without a dual-write phase.** Generating a new secret and immediately using it without a verification window means any misconfiguration causes instant failures with no rollback path.
- **No verification after rotation.** Declaring rotation complete without confirming the old secret is actually rejected leaves false confidence. Test both directions: new secret works, old secret is rejected.
- **Storing rotation history in source control.** Git history of secret names, values, or rotation events belongs in your audit logging system — not in a CHANGELOG or commit message. A commit referencing a specific credential value leaks it permanently.
- **Batching all consumer updates together.** Updating all services simultaneously means a misconfiguration affects everything at once. Update and verify one service at a time so failures are isolated.
- **Skipping incident-driven rotation because "it was probably nothing."** Any suspected exposure must be treated as confirmed exposure. The cost of an unnecessary rotation is far lower than the cost of an active breach.
