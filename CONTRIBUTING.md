# Contributing to agentic-dev-framework

Thank you for improving the framework. This guide covers the rules for changes that keep the framework trustworthy and consistent.

---

## Table of Contents
- [Agent Change Rules](#agent-change-rules)
- [Skill Addition Rules](#skill-addition-rules)
- [Count Consistency Rule](#count-consistency-rule)
- [Token Naming Convention](#token-naming-convention)
- [Release Process](#release-process)
- [What Will Be Rejected](#what-will-be-rejected)

---

## Agent Change Rules

Any PR that adds, modifies, or removes an agent file (`.github/agents/*.agent.md`) must:

1. **Include a full Review Council assessment.** Run `@review-council` on the proposed agent definition. Include the council output in the PR description or as a linked comment.
2. **Include a Guardian security assessment.** The Guardian perspective must explicitly assess: Does this agent have access to secrets? Does it have scope boundaries that prevent privilege escalation? Are its constraints specific enough to be enforceable?
3. **Define all four required agent sections:** Mission, Focus Areas, Constraints, Scope Boundaries ("What This Agent Does NOT Do"). A PR missing any section will be rejected.
4. **Not weaken existing agent constraints.** Removing or softening a constraint requires explicit justification in an ADR (`docs/adr/`).
5. **Update `copilot-instructions.md`** to reflect the new/modified agent in the Agents section and the Agent Invocation section.
6. **Update `README.md`** agent table.
7. **Update the agent count** in all three count locations (see [Count Consistency Rule](#count-consistency-rule)).

---

## Skill Addition Rules

Any PR that adds a new skill file (`.github/skills/*.md`) must include all of these sections:

| Section | Required Content |
|---------|-----------------|
| YAML frontmatter | `name` (kebab-case) and `description` (one sentence) |
| `## When to Use` | Specific trigger conditions — when should an agent invoke this skill? |
| `## Procedure` | Numbered steps (minimum 5) |
| `## Checklist` | Checkboxes for verification items |
| `## Anti-Patterns` | At least 3 things to avoid, with explanation of why |

Additional rules:
- Skill file name must match the `name` frontmatter value (kebab-case, `.md` extension).
- Skills must be flat files in `.github/skills/` — no subfolders.
- Skill names must be kebab-case (e.g., `api-design.md`, not `ApiDesign.md` or `api_design.md`).
- A skill that is entirely technology-specific must use `{{TOKEN}}` placeholders for all technology names.
- Update `copilot-instructions.md` Domain Skills section.
- Update `README.md` skills table.
- Update the skill count in all three count locations (see [Count Consistency Rule](#count-consistency-rule)).
- Add any new `{{TOKEN}}` placeholders introduced by the skill to `TOKENS.md`.

---

## Count Consistency Rule

The framework tracks agent and skill counts in **three locations**. Every PR that adds or removes an agent or skill file **must update all three locations** or the PR will be rejected.

### The Three Count Locations

| Location | What to Update |
|----------|----------------|
| `.github/copilot-instructions.md` line 3 | Tagline: `X specialized agents and Y domain skills` |
| `.github/copilot-instructions.md` Domain Skills section | `Y skills across N categories` |
| `README.md` | All occurrences of the skill/agent count |

### How to Verify Before Opening a PR

```bash
# Count agent files
ls .github/agents/*.agent.md | wc -l

# Count skill files
ls .github/skills/*.md | wc -l

# Check that both counts match what's declared in copilot-instructions.md
grep "specialized agents and" .github/copilot-instructions.md

# Check README.md counts match
grep "domain skills\|specialized agents" README.md
```

All four numbers must agree. If they don't, fix the counts before pushing.

---

## Token Naming Convention

All `{{TOKEN}}` placeholders must follow these rules:

1. **Format**: `{{SCREAMING_SNAKE_CASE}}` — all caps, underscores, double braces.
2. **Naming**: Descriptive enough to understand without context. `{{SERVER_FRAMEWORK}}` ✓, `{{THING}}` ✗.
3. **Registration**: Every new token introduced in any PR must be added to `TOKENS.md` with a description and example values before the PR can merge.
4. **No duplicates**: Before creating a new token, check `TOKENS.md` to see if a suitable token already exists.
5. **Technology names**: Use the category level, not the specific technology. `{{ORM}}` ✓, `{{PRISMA_OR_TYPEORM}}` ✗.
6. **Booleans / switches**: Do not use tokens for on/off switches. Use conditional sections or comments instead.

### Token Registration Format (TOKENS.md)

```markdown
| `{{MY_NEW_TOKEN}}` | Description of what this token represents | `Example Value 1`, `Example Value 2` |
```

---

## Release Process

The framework uses semantic versioning. See [docs/VERSIONING.md](docs/VERSIONING.md) for the full versioning policy.

### Before Cutting a Release

1. Ensure all PRs for the milestone are merged.
2. Run the framework integrity CI: `gh workflow run framework-integrity.yml`
3. Update `CHANGELOG.md` with all changes since the last release.
4. Update the version number in `.github/copilot-instructions.md` footer (`## Framework Version`).
5. Update `framework-manifest.json` with the new version and any changed file versions.
6. Tag the release: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`
7. Push the tag: `git push origin vX.Y.Z`
8. Create a GitHub Release from the tag with the CHANGELOG section as the body.

### Hotfix Process

For security fixes or critical bugs in a released version:
1. Branch from the release tag: `git checkout -b fix/critical-issue vX.Y.Z`
2. Apply the minimal fix.
3. Skip the full release checklist but DO run framework integrity CI.
4. Tag as a PATCH release: `vX.Y.Z+1`

---

## What Will Be Rejected

The following changes will be **rejected without review** (close immediately):

### Security Weakening
- Any PR that removes or softens a constraint in the "Constraints (Non-Negotiable)" section of `copilot-instructions.md`.
- Any PR that removes the Guardian perspective from the Review Council agent.
- Any PR that adds a bypass or exception to the "No secrets in logs" rule.
- Any PR that weakens OAuth/PKCE requirements.

### Completion Gate Weakening
- Any PR that removes or loosens the completion gate logic from any agent or instruction file.
- Any PR that redefines "done" to mean "implemented" rather than "verified in user-visible path."
- Any PR that removes the explicit blocker surfacing requirement.

### Verification Weakening
- Any PR that removes the mandatory smoke/verification command requirement.
- Any PR that reduces the test coverage target below 80% floor.
- Any PR that removes the "Verification Categories" labels (`VERIFIED-AUTOMATED`, `VERIFIED-MANUAL`, etc.) from testing instructions.

### Structural Violations
- Skills in subfolders (`.github/skills/subdir/skill.md`).
- Agent files not ending in `.agent.md`.
- PRs with unregistered `{{TOKEN}}` placeholders (not in `TOKENS.md`).
- PRs that update counts in fewer than all 3 count locations.
- PRs where agent files are missing any of the 4 required sections.

### Process Violations
- Agent PRs missing a Review Council assessment.
- Agent PRs where the Guardian perspective is absent or generic ("looks fine").
- PRs opened with known failing CI checks (unless explicitly approved and documented as blocked).
