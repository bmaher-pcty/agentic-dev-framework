---
name: db-backup-recovery
description: 'Database backup and recovery procedure including backup verification (not just creation), PITR, RTO testing, and backup storage security.'
---

# Database Backup & Recovery

## When to Use
- Setting up a new environment (ensure backup is configured from day one).
- Verifying backup integrity before a major release or destructive migration.
- Conducting a disaster recovery drill.
- Before applying any destructive migration (bulk delete, column drop, table drop).
- Investigating whether your backup can actually be used to recover — if this has never been tested, do it now.

## Procedure

### 1. Backup Creation
1. Configure automated daily backups in your database platform (RDS automated backups, Cloud SQL scheduled backups, or equivalent).
2. Verify the backup schedule is active — do not rely on the configuration UI alone. Check that backup jobs are completing by reviewing backup history.
3. For manual backups before destructive operations: trigger a manual snapshot and confirm it appears in your backup list before proceeding.
4. Store backups in a storage account that is **separate from the application account** (see Backup Storage Security).

### 2. Backup Verification (Critical)
> **A backup that has never been restored is untested.** File existence in a backup bucket does not mean the backup is valid or restorable. Verification means restore, not existence check.

Verification procedure:
1. Restore the backup to a staging or isolated database instance — not the production database.
2. Run representative smoke queries against the restored database: row counts, recent-record checks, foreign key integrity.
3. Confirm the application can connect and run successfully against the restored data (optional smoke test with a staging deploy pointing at the restored DB).
4. Record the verification result: date, backup timestamp used, result (pass/fail), and any anomalies.
5. Delete the verification instance after confirming results to avoid orphaned resources.

**Schedule:** Run backup verification at least monthly, and before every major release.

### 3. Point-in-Time Recovery (PITR) Configuration
1. Enable WAL archiving (PostgreSQL) or binary log replication (MySQL/Aurora) at the database level.
2. Set the PITR retention window based on your RPO target (minimum: 7 days; recommended: 35 days for regulated environments).
3. Test recovery to a specific timestamp at least quarterly:
   - Restore to a point 2 hours before "now" in a staging environment.
   - Confirm data state matches expectations for that timestamp.
   - Document the RPO achieved (how much data could have been lost in a worst-case scenario).
4. Document your RPO and RTO targets explicitly (see RTO Testing).

### 4. Backup Storage Security
1. Encrypt all backups at rest using keys managed in your key management service (KMS), not in the same account as the backup files.
2. Store backups in a separate cloud account or storage project from the application — a compromised application account must not be able to delete or access backups.
3. Apply least-privilege access to the backup storage: only the backup service account and authorized humans should have read access.
4. Test access controls: confirm that application service accounts cannot read or delete backup files.
5. Rotate backup encryption keys per the `#secrets-rotation` schedule (annually for KMS CMKs unless incident-driven).

---

## Backup Verification Protocol (Expanded)

The most common backup failure mode is discovering the backup is corrupted or incomplete during an actual disaster. Prevent this with structured verification:

| Check | How | Pass Condition |
|-------|-----|----------------|
| Restore succeeds | Trigger restore to staging DB | No errors during restore |
| Data completeness | Count rows in key tables | Counts within expected range |
| Recent data present | Query records from last 24h | Returns expected recent records |
| Foreign key integrity | Run integrity check query | No orphaned records |
| Application connectivity | Point staging app at restored DB | App connects and serves requests |
| Documented result | Record date, backup timestamp, result | Written record exists |

---

## PITR (Point-in-Time Recovery)

### PostgreSQL WAL Archiving Setup
- Enable `archive_mode = on` in `postgresql.conf`.
- Set `archive_command` to copy WAL files to your backup storage.
- Verify WAL files are being written to backup storage after enabling.
- Test recovery by restoring a base backup plus WAL files to a specific LSN or timestamp.

### MySQL / Aurora Binlog Replication
- Enable binary logging: `log_bin = ON` in MySQL, or enable automated backups in Aurora (which enables binlog automatically).
- Set `expire_logs_days` to retain logs for your PITR window.
- Test PITR by restoring to a specific GTID or timestamp in a staging environment.

### RPO and RTO Definitions
- **RPO (Recovery Point Objective):** Maximum acceptable data loss — how old can the most recent backup be when recovery starts? Define in hours. Example: RPO = 1 hour means any data written in the last hour before an outage may be lost.
- **RTO (Recovery Time Objective):** Maximum acceptable downtime — how long can the system be unavailable while recovering? Define in hours. Example: RTO = 4 hours means recovery must complete within 4 hours of declaring a disaster.

Document both values in your runbook. They constrain your backup frequency and infrastructure choices.

---

## RTO Testing

**Simulate a full outage recovery drill at least twice per year:**

1. Declare a "simulated disaster" in a staging environment — destroy the database.
2. Start a timer.
3. Follow the documented recovery runbook from scratch: restore from backup, restore WAL/binlog, reconnect the application, verify with smoke tests.
4. Stop the timer when the application is verified as serving traffic from restored data.
5. Compare elapsed time against your RTO target.
6. Document the result: actual recovery time, any steps that were slow or unclear, runbook gaps identified.
7. If actual recovery time exceeds RTO target, file a task to address the gap before the next drill.

> If you have never run an RTO drill, your RTO is unknown — not zero. Assume it is longer than you expect.

---

## Retention Policy

Define retention tiers to balance cost and recovery capability:

| Tier | Frequency | Retention Window | Use Case |
|------|-----------|-----------------|----------|
| **Hot** | Daily | 7–35 days | Recent error recovery, daily PITR |
| **Warm** | Weekly | 90 days | Monthly rollback, compliance audits |
| **Cold** | Monthly/Annual | 1–7 years | Regulatory compliance, long-term audit |

- Define the retention policy explicitly in your infrastructure configuration — do not rely on manual cleanup.
- Enforce retention limits: delete backups older than the policy allows to avoid unbounded storage cost.
- Confirm cold storage is readable before retention is extended — some archive formats become unreadable over time.

---

## Checklist

- [ ] Automated backup schedule configured and verified as running (check backup history, not just configuration).
- [ ] **Backup restoration tested** — not just file existence. Restore verified to staging DB with smoke queries.
- [ ] Backup verification scheduled on a recurring basis (monthly minimum).
- [ ] PITR enabled (WAL archiving or binlog replication active).
- [ ] RPO and RTO targets documented in the runbook.
- [ ] RTO drill completed — actual recovery time measured and compared against target.
- [ ] Backups stored in a separate account from the application.
- [ ] Backups encrypted at rest with KMS-managed keys stored separately from backup files.
- [ ] Access controls tested — application service accounts cannot access or delete backup storage.
- [ ] Retention policy defined and enforced in infrastructure configuration.

---

## Anti-Patterns

- **Treating backup creation as sufficient (never tested restore).** The most dangerous backup anti-pattern. A backup job running successfully does not mean the backup can be used for recovery. Test restoration regularly — this is the only way to know your backup works.
- **Backing up to the same account as the application.** If the application account is compromised or the application itself triggers a catastrophic deletion, backups in the same account are at risk. Backup storage must be in a separate account with separate access controls.
- **Storing backup encryption keys next to the backups.** An encrypted backup whose decryption key is stored in the same location is not meaningfully encrypted. The key must be in a separate key management system that is independently secured.
- **Not testing PITR.** Enabling WAL archiving or binlog replication and never testing recovery to a specific timestamp means you don't know whether PITR actually works. Test it — PITR setups have subtle failure modes (WAL gaps, archive command errors) that only appear when you try to use them.
- **No documented RTO target.** If there is no documented RTO, teams cannot make infrastructure decisions (instance size, replication topology, backup frequency) that are calibrated to recovery requirements. Document the RTO before an incident forces you to invent one under pressure.
- **Manual, ad-hoc backup verification.** "We checked it once when we set it up" is not a verification schedule. Backups degrade — data formats change, credentials expire, storage configurations drift. Verification must be automated and recurring.
