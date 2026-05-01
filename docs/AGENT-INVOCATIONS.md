# Agent Invocation Examples

Per-agent, per-project-type invocation examples for the agentic-dev-framework.
Consult this file when learning how to invoke an agent or when onboarding a new team.

Agents are invoked via `@<agent-name>:` in GitHub Copilot Chat.

---

## Product Manager (`@pm`)

```
# Web app
@pm: Should we support bulk import? What's the user need?
@pm: Is this a priority 1 feature for the next milestone?

# CLI/service
@pm: Does this CLI flag belong in the core command or a subcommand?
@pm: What's the acceptance criterion for the background sync feature?

# Data pipeline
@pm: Should the pipeline expose partial results or only complete runs?
@pm: What SLA is acceptable for the daily report job?
```

---

## System Architect (`@architect`)

```
# Web app
@architect: Design the session management flow (JWT + refresh tokens)
@architect: How should we structure the third-party sync service?

# CLI/service
@architect: Design the plugin system for this CLI tool
@architect: How should config files be discovered and merged?

# Data pipeline
@architect: Design the incremental processing strategy for large datasets
@architect: How should we handle partial failures in the pipeline?
```

---

## Engineer (`@engineer`)

```
# Web app
@engineer: Implement the health check endpoint
@engineer: Add input validation for user creation

# CLI/service
@engineer: Implement the --dry-run flag for the deploy command
@engineer: Add retry logic to the API client

# Data pipeline
@engineer: Implement the deduplification step in the ETL pipeline
@engineer: Add progress reporting to the batch processor
```

---

## Veteran QA (`@qa`)

```
# Web app
@qa: Design test plan for the OAuth integration
@qa: What edge cases should we cover for authentication?

# CLI/service
@qa: What failure modes need coverage for the file watcher?
@qa: Design regression tests for the config parsing logic

# Data pipeline
@qa: What data quality checks belong in the pipeline?
@qa: Design tests for the deduplication logic edge cases
```

---

## Bold UX Designer (`@designer`)

```
# Web app
@designer: Create the dashboard layout (header, sidebar, main)
@designer: Is this button styling accessible?

# CLI/service
@designer: Review the CLI output format for readability
@designer: Is the error message hierarchy clear to operators?

# Data pipeline
@designer: Review the progress display for long-running jobs
@designer: Is the report output format readable for end users?
```

---

## Technical Writer (`@technical-writer`)

```
@technical-writer: Consolidate duplicate markdown and propose canonical docs
@technical-writer: Audit script references and propose safe deletions with replacements
```

---

## DevOps/Infrastructure (`@devops-infrastructure`)

```
# Web app
@devops-infrastructure: Add health checks to {{CONTAINER_COMPOSE_FILE}} services
@devops-infrastructure: Harden {{REVERSE_PROXY}} security headers
@devops-infrastructure: Debug why the api container fails to start

# CLI/service
@devops-infrastructure: Set up the CI pipeline for the CLI build
@devops-infrastructure: Configure cross-platform build matrix

# Data pipeline
@devops-infrastructure: Configure the job scheduler container
@devops-infrastructure: Optimize the pipeline container resource limits
```

---

## Performance (`@performance`)

```
# Web app
@performance: Analyze why the /metrics endpoint is slow
@performance: Find N+1 queries in the data service
@performance: Reduce the frontend bundle size

# CLI/service
@performance: Profile startup time for the CLI tool
@performance: Find the bottleneck in the file processing loop

# Data pipeline
@performance: Find why the transformation step is slow on large datasets
@performance: Optimize the database batch insert performance
```

---

## Accessibility (`@accessibility`)

```
# Web app
@accessibility: Audit the Settings page for WCAG 2.1 AA compliance
@accessibility: Fix keyboard navigation in the multi-step wizard
@accessibility: Add {{A11Y_TESTING_LIB}} tests to the login flow

# CLI/service
@accessibility: Review error message clarity for screen reader users
@accessibility: Check that color is not the only indicator in terminal output

# Data pipeline
@accessibility: Review the report output for accessibility of exported formats
```

---

## Review Council (`@review-council`)

```
@review-council: Full code review before PR merge
@review-council: Audit this prompt for quality and completeness
@review-council: Review the auth middleware changes
```

---

## Innovator (`@innovator`)

```
# Web app
@innovator: We keep adding caching layers to the API — are we solving the wrong problem?
@innovator: Three different teams have proposed three different session management approaches. What would a 10x better design look like?
@innovator: Before we commit to this real-time sync architecture, what alternatives exist?

# CLI/service
@innovator: The plugin system design feels accidental — is there a better structural model?
@innovator: We've tried two config file formats and neither feels right. What does prior art suggest?

# Data pipeline
@innovator: Our ETL pipeline is getting complex. Are we solving the right problem, or is the data model the issue?
@innovator: Before we build a custom scheduler, what approaches exist for this class of problem?
```

---

## Researcher (`@researcher`)

```
# Council / any agent — commissioning research
@researcher: What are the documented tradeoffs of approach X vs. Y in production at scale?
@researcher: What does the evidence say about how teams handle problem P, and what perspectives am I missing?
@researcher: Survey the security landscape on threat vector Z before the Guardian issues a finding.

# Proactive use — Researcher offers unsolicited findings
@researcher: I noticed the discussion is missing the end-user / compliance perspective — here are findings you may not have considered.
@researcher: Prior research exists on this topic; here is the brief before you proceed.
```
