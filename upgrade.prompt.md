---
name: "Upgrade agentic-dev-framework"
description: "Structured upgrade from a previous framework version to the current version. Preserves your token-resolved values and project-specific configuration."
argument-hint: "Provide your current framework version (e.g., v1.1.0) and the target version (e.g., v1.2.0). Have both the framework repo and your bootstrapped project open."
---

# Framework Upgrade: agentic-dev-framework

> **Use this prompt when upgrading to a newer version of the framework.**
> **Do NOT re-run BOOTSTRAP.prompt.md for upgrades. Bootstrap is for initial setup only — re-running it will overwrite your resolved token values.**

---

## Phase 1: Pre-Upgrade Assessment

Before making any changes, establish a baseline.

### Step 1.1 — Identify Your Current Version

Read the version line from `docs/FRAMEWORK_SETUP.md` or `README.md` in your project. If neither exists, check `.github/copilot-instructions.md` for the framework version footer.

If no version is found, treat your current version as unknown and proceed cautiously — mark all file comparisons as requiring human review.

### Step 1.2 — Read the CHANGELOG

Read `CHANGELOG.md` from the new framework version. Identify every change between your current version and the target version.

### Step 1.3 — Classify Each Change

Classify every changed file as one of:

| Type | Definition | Risk |
|------|------------|------|
| **Additive** | New files, new sections appended to existing files, new skills or prompts | Low — apply without risk |
| **Modifying** | Changes to existing sections in existing files | Medium — requires token-aware merge |
| **Breaking** | Changes to file structure, token names, agent interfaces, or frontmatter schema | High — requires manual decision |
| **Structural** | Changes to `copilot-instructions.md`, agent frontmatter, or instruction `applyTo` patterns | High — apply carefully, verify after |

Present the classified list to the user before proceeding to Phase 2.

---

## Phase 2: Token-Aware Diff

For each **Modifying**, **Breaking**, or **Structural** file in the new framework version:

1. Read the new version of the file from the framework.
2. Read your bootstrapped version of the file from your project's `.github/`.
3. Identify which differences are:
   - **Framework improvements** (new rules, corrected logic, new sections) → to apply
   - **Your token-resolved values** (your actual stack names, commands, paths) → to preserve
4. Produce a human-readable patch showing exactly what would change.

**The cardinal rule: Never replace a resolved token value with a `{{TOKEN}}` placeholder.**

For each modified file, show:

```
### File: .github/[path/to/file.md]
Change type: [Modifying / Breaking / Structural]

**What changed in the new framework version:**
[Brief description of the framework-side change]

**Your current version:**
[Excerpt of affected section]

**Proposed merge result:**
[Excerpt showing framework improvement applied with your token values preserved]

**Action required:** [Apply automatically / Requires your decision]
```

Wait for the user to confirm each Breaking or Structural change before applying it.

---

## Phase 3: Staged Application

Apply changes in this order to minimize merge conflicts and risk:

1. **New files first** — Copy all Additive files directly. No merge conflicts possible.
2. **New sections in existing files** — Append to the correct location within the file.
3. **Modified sections in existing files** — Apply the token-aware merge for each Modifying file.
4. **Updated frontmatter and `applyTo` patterns** — Apply last and confirm with user. These affect which files agents see and have the broadest impact.

For each file applied, output: `✓ Applied: [file path] ([change type])`

For each file skipped or deferred, output: `⏸ Deferred: [file path] — [reason / what human must decide]`

---

## Phase 4: Post-Upgrade Verification

After all changes are applied, run these verification steps:

### Check 1: No Token Regression
```bash
grep -r '{{[A-Z_]*}}' .github/ --include="*.md" --include="*.yml"
```
Expected: Zero matches (or only intentional unresolved tokens listed in FRAMEWORK_SETUP.md).

### Check 2: Agent Count Consistent
```bash
ls .github/agents/*.agent.md | wc -l
```
Confirm the count matches the new framework version's declared agent count.

### Check 3: Smoke Command Intact
Confirm your `{{SMOKE_COMMAND}}` value (resolved during bootstrap) is still present and unchanged in `testing.instructions.md` and `FRAMEWORK_SETUP.md`.

### Check 4: FRAMEWORK_SETUP.md Updated
Update `docs/FRAMEWORK_SETUP.md` with:
- New framework version
- Upgrade date
- List of files changed in this upgrade

---

## Output Format

After all phases complete, present this report:

```markdown
## Upgrade Report: v[OLD] → v[NEW]

### Changes Applied
| File | Change Type | Summary |
|------|-------------|---------|
| [file] | [Additive/Modifying/Breaking/Structural] | [what changed] |

### Changes Requiring Human Decision
| File | What Changed | Decision Required |
|------|-------------|------------------|
| [file] | [description] | [what the human must decide] |

### Preserved Values
- Token values preserved (not overwritten by framework tokens):
  - [token] = [your value] ✓

### Post-Upgrade Verification
- ✅/❌ Token regression check: [result]
- ✅/❌ Agent count: [result]
- ✅/❌ Smoke command intact: [result]
- ✅/❌ FRAMEWORK_SETUP.md updated: [result]

### Next Steps
[Any manual steps required before committing the upgrade]
1. Review deferred files: [list]
2. Commit: `git add .github/ docs/FRAMEWORK_SETUP.md && git commit -m "chore: upgrade agentic-dev-framework to v[NEW]"`
3. Run smoke test to confirm nothing regressed: `[your SMOKE_COMMAND]`
```

---

## Rules (Non-Negotiable)

- Do NOT replace resolved token values with `{{TOKEN}}` placeholders at any point.
- Do NOT apply Breaking or Structural changes without explicit user confirmation.
- Do NOT claim the upgrade is complete while token regression checks show unresolved tokens.
- Do NOT soften any security constraint that was strengthened in the new version.
- Do NOT remove the completion gate logic or the "invisible success is a defect" rule from any updated file.
- ALWAYS update `docs/FRAMEWORK_SETUP.md` with the new version and upgrade date before declaring completion.
