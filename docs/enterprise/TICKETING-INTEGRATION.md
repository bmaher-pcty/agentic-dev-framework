# Ticketing System Integration

The framework's `docs/open-handoffs.md` is the local tracking file for cross-agent escalations. It is designed for in-session agent context — agents read it at the start of an invocation to understand unresolved decisions without requiring chat history.

For enterprise teams, significant escalations should also be reflected in your organization's ticketing system. This document describes integration patterns for GitHub Issues (automated), Jira (manual), and Azure DevOps (manual).

---

## When to Create a Ticket

Not every handoff entry needs a ticket. Create a ticket when the handoff:

- Requires more than one person (human or cross-team) to resolve
- Remains unresolved across **multiple PRs or sessions**
- Has **security or compliance implications**
- Blocks a release or milestone

Routine agent-to-agent handoffs (e.g., Engineer escalates a design question to Architect within the same session) do not need external tickets.

---

## GitHub Issues Integration (Automated)

The `handoff-sync.yml` workflow in this directory automates GitHub Issue creation and closure from `docs/open-handoffs.md`. It runs on every push to `main` that modifies the handoffs file.

### Setup

1. Copy `docs/templates/ci/handoff-sync.yml` to `.github/workflows/handoff-sync.yml` in your project.
2. Ensure the workflow has `issues: write` permission (already set in the template).
3. The workflow creates the `agent-handoff` label automatically on first run.

### How It Works

| Handoff State | Issue Action |
|--------------|--------------|
| `[OPEN]` — new entry | Creates a new GitHub Issue with the SBAR content and `agent-handoff` label |
| `[OPEN]` — re-opened | Re-opens the existing closed Issue |
| `[RESOLVED]` — newly resolved | Posts a resolution comment and closes the Issue |

### Handoff Format Requirements

For the sync to parse entries correctly, `docs/open-handoffs.md` must use this SBAR format:

```markdown
## Handoff: <Title>
**Status:** [OPEN]
**From:** <agent name>
**To:** <agent name>
**Situation:** <one-sentence description of what triggered this escalation>
**Background:** <relevant context>
**Assessment:** <what the sending agent determined>
**Decision Needed:** <specific question or decision required>
**Resolution:** (leave blank until resolved)
```

When resolving a handoff, change `[OPEN]` to `[RESOLVED]` and fill in the `**Resolution:**` field. The sync workflow will close the corresponding Issue on next push to `main`.

---

## Jira Integration (Manual Process)

### Creating a Handoff Ticket

1. When creating an `[OPEN]` entry in `docs/open-handoffs.md`, create a corresponding Jira ticket with:
   - **Issue type:** Task (or your team's equivalent for non-bug work items)
   - **Label:** `agent-handoff`
   - **Summary:** `[Agent Handoff] <title from handoffs file>`
   - **Description:** Copy the Situation, Background, Assessment, and Decision Needed fields from the handoff entry
2. Add the Jira ticket number to the handoff entry:
   ```
   **Jira:** [PROJ-1234](https://yourcompany.atlassian.net/browse/PROJ-1234)
   ```

### Resolving a Handoff Ticket

1. Change `[OPEN]` to `[RESOLVED]` in `docs/open-handoffs.md` and fill in `**Resolution:**`.
2. In Jira: transition the ticket to Done and add a comment with the commit or PR that resolved it.
3. If the handoff blocked a PR, link the Jira ticket to the PR before merge.

### Jira Automation (Optional)

If your organization uses Jira Automation, you can trigger ticket creation via a webhook fired by a GitHub Actions workflow that detects new `[OPEN]` entries — this mirrors the GitHub Issues automation pattern above. Consult your Jira administrator for the webhook receiver configuration.

---

## Azure DevOps Integration (Manual Process)

### Creating a Handoff Work Item

1. When creating an `[OPEN]` entry in `docs/open-handoffs.md`, create a corresponding Azure DevOps Work Item with:
   - **Work Item type:** Task (or your team's process equivalent)
   - **Tag:** `agent-handoff`
   - **Title:** `[Agent Handoff] <title from handoffs file>`
   - **Description:** Copy the SBAR fields from the handoff entry
2. Add the Work Item ID to the handoff entry:
   ```
   **ADO:** [#1234](https://dev.azure.com/yourorg/yourproject/_workitems/edit/1234)
   ```

### Resolving a Handoff Work Item

1. Change `[OPEN]` to `[RESOLVED]` in `docs/open-handoffs.md` and fill in `**Resolution:**`.
2. In Azure DevOps: transition the Work Item to Closed/Done and link the resolving PR using the `Fixed by` or `Resolved by` relation.

### Azure DevOps Pipeline Integration (Optional)

Azure Pipelines can trigger work item creation via the Azure DevOps REST API in a pipeline task. If you want automated ticket creation, run a script task in your pipeline after detecting new `[OPEN]` entries:

```bash
# Example: Create a Work Item via Azure DevOps REST API
# Requires: $AZURE_DEVOPS_PAT (Personal Access Token) with Work Items: Read & Write scope
az boards work-item create \
  --title "[Agent Handoff] ${HANDOFF_TITLE}" \
  --type Task \
  --assigned-to "" \
  --description "${HANDOFF_SBAR_CONTENT}" \
  --org "https://dev.azure.com/${YOUR_ORG}" \
  --project "${YOUR_PROJECT}"
```

Store `AZURE_DEVOPS_PAT` as a pipeline secret variable — never hardcode it.

---

## Anti-Pattern: Don't Over-Ticket

The handoffs file tracks all agent escalations, including routine ones resolved within the same session. Do **not** create external tickets for every entry — this creates noise that erodes trust in your ticketing system.

**Create external tickets only for handoffs that are:**
1. Unresolved after the session that created them ends
2. Security or compliance related
3. Blocking a release, milestone, or another team's work
4. Requiring cross-team human coordination

All other handoffs are resolved locally in `docs/open-handoffs.md` without ticketing overhead.
