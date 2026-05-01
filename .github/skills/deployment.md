---
name: deployment
description: 'Deployment verification protocol, rollback procedure, and blue-green/canary patterns for safe production releases.'
---

# Deployment

## When to Use
- Before any production deployment.
- After infrastructure changes that affect service delivery (networking, IAM, secrets).
- Setting up or executing blue-green or canary deployment patterns.
- Diagnosing a live incident that may require rollback.

## Procedure

### Pre-Deployment Checklist
1. Confirm the smoke test suite passes on the deployment artifact (not just on `main`).
2. Review pending database migrations: are they backward-compatible? Can the old version still run against the new schema?
3. Verify secrets referenced by the new version are provisioned in the target environment. Do not deploy if a required secret is missing.
4. Confirm rollback artifact is identified and accessible (previous image tag, release tag, or deployment revision ID).
5. Notify on-call and relevant stakeholders of the deployment window.

### Deployment Execution
1. Trigger deployment via CI/CD pipeline — never deploy manually to production.
2. Monitor the pipeline for errors before instances start serving traffic.
3. Watch for configuration errors, secret-mount failures, and container crash loops during the rollout.
4. Do not mark the deployment complete until all instances have passed their health checks.

### Post-Deployment Verification Protocol
1. Run smoke tests against the production environment within 5 minutes of rollout completing.
2. Check application error rate in your observability dashboard. Compare against the pre-deployment baseline for at least 5 minutes.
3. Confirm health check endpoints (`/health`, `/ready`) return 200 for all instances.
4. Spot-check at least one user-visible workflow end-to-end (not just an API ping).
5. Monitor for at least 10 minutes before declaring the deployment successful.

---

## Rollback Procedure

### When to Trigger
Initiate rollback immediately if any of the following occur post-deployment:
- Error rate exceeds 2× pre-deployment baseline.
- Any health check endpoint returns non-200 for more than 60 seconds.
- User-visible functionality is broken (reported via monitoring or user report).
- A secret or configuration is missing and cannot be hot-patched without re-deploying.

### Rollback Steps
1. Announce rollback in the incident channel — do not roll back silently.
2. Trigger rollback via CI/CD to the last known-good revision (do not manually revert).
3. Confirm rollback pipeline completes without errors.
4. Verify all instances pass health checks after rollback.
5. Run smoke tests post-rollback to confirm stability.
6. Do NOT close the incident until post-rollback verification passes.

### Post-Rollback Actions
1. Retain logs from the failed deployment for root-cause analysis.
2. File an incident report documenting: what broke, when rollback was triggered, how long the degraded window was.
3. Block the next deployment of the same artifact until root cause is identified.

---

## Blue-Green Pattern

### Traffic Switching Procedure
1. Deploy new version to the green environment. Do NOT switch traffic yet.
2. Run smoke tests against the green environment directly (bypass the load balancer).
3. Verify database migrations are applied and green instances are healthy.
4. Switch 5% of traffic to green; monitor error rate and latency for 2 minutes.
5. If metrics are clean, switch 100% of traffic to green.
6. Maintain the blue environment in standby for at least 30 minutes before decommissioning.

### Validation Before Full Cutover
- Error rate on green must be ≤ error rate on blue.
- P99 latency on green must be ≤ 110% of P99 latency on blue.
- All health checks on green must return 200.

### Cleanup of Old Environment
- Do not tear down the blue environment until the deployment is declared stable (≥30 minutes at 100% traffic).
- Tag the blue snapshot with its deployment ID before deleting — preserve it for 48 hours.

---

## Canary Pattern

### Traffic Increment Schedule

| Phase | Traffic % | Minimum Hold Time | Gate Condition |
|-------|-----------|-------------------|----------------|
| Canary start | 1% | 5 minutes | Error rate stable |
| Expand | 10% | 10 minutes | Error rate < 1.5× baseline |
| Half | 50% | 15 minutes | Error rate < 1.2× baseline |
| Full | 100% | 10 minutes post-rollout | Error rate ≤ baseline |

### Metrics Gates Between Increments
- HTTP 5xx rate: must not exceed 1.5× pre-deployment baseline.
- P95 response time: must not exceed 120% of pre-deployment P95.
- Health check: all canary instances returning 200.

### Automatic Rollback Triggers
Configure your deployment platform to roll back automatically if:
- Error rate exceeds 3× baseline at any canary stage.
- P99 latency exceeds 2× baseline at any canary stage.
- Any health check fails for more than 120 consecutive seconds.

---

## Checklist

### Pre-Deployment
- [ ] Smoke tests pass on the deployment artifact.
- [ ] Database migrations reviewed for backward compatibility.
- [ ] All required secrets verified as provisioned in target environment.
- [ ] Rollback artifact identified and accessible.
- [ ] On-call and stakeholders notified.

### Deployment
- [ ] Deployment triggered via CI/CD pipeline (not manually).
- [ ] All instances passed health checks before traffic was accepted.
- [ ] No configuration or secret-mount errors in deployment logs.

### Post-Deployment
- [ ] Smoke tests ran against production and passed.
- [ ] Error rate compared against pre-deployment baseline for ≥10 minutes.
- [ ] At least one user-visible workflow verified end-to-end.
- [ ] Deployment declared stable only after all checks passed.

### Rollback-Ready
- [ ] Rollback procedure is documented and reachable from the runbook.
- [ ] Team knows how to trigger rollback without the original deployer.
- [ ] Previous release artifact is accessible and tested.

---

## Anti-Patterns

- **Deploying without health checks configured.** If there is no health check, there is no signal that the deployment succeeded. An instance can be running but serving 500s — configure `/health` and `/ready` endpoints before every deployment.
- **Skipping smoke tests in production.** Running smoke tests only in staging is insufficient. A deployment is not verified until smoke passes in the environment receiving real traffic.
- **No rollback plan.** "We'll figure it out if something breaks" is not a plan. Identify the rollback artifact and verify the procedure before deployment begins.
- **Deploying to all instances simultaneously.** A full fleet rollout with no canary or phased ramp means a bad deploy affects 100% of users immediately. Use rolling, canary, or blue-green — never simultaneous.
- **Declaring success before the monitoring window closes.** Error rates and latency anomalies can take several minutes to materialize. Waiting only 60 seconds before closing the deployment is not sufficient.
- **Manual production deployments.** Manual deploys bypass audit trails, are not reproducible, and cannot be rolled back reliably. All production deployments must go through the CI/CD pipeline.
