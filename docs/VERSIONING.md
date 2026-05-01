# Framework Versioning Policy

agentic-dev-framework uses [Semantic Versioning 2.0.0](https://semver.org/).

---

## Version Format

`MAJOR.MINOR.PATCH` — for example, `1.2.3`

---

## Version Increment Rules

### MAJOR (breaking changes)

Increment MAJOR when a change requires adopters to manually update their tailored `.github/` folders:

- Removing a token from `TOKENS.md` without a migration path.
- Renaming an agent file or changing its required frontmatter fields.
- Renaming a skill file or changing its required section structure.
- Changing the `applyTo` contract in instruction files in a way that breaks existing glob patterns.
- Removing or significantly restructuring the BOOTSTRAP.prompt.md phase flow.
- Changing the YAML frontmatter schema for agents, skills, or instructions.

### MINOR (new capabilities, backward-compatible)

Increment MINOR when adding new agents, skills, instructions, prompts, or tokens without removing or breaking existing ones:

- Adding a new agent file.
- Adding a new skill file.
- Adding new tokens to `TOKENS.md`.
- Adding new sections to existing agents or skills (additive changes).
- Adding new prompts or instruction files.
- Expanding the bootstrap interview questions.

### PATCH (bug fixes and clarifications)

Increment PATCH for changes that correct errors without adding or removing capabilities:

- Fixing typos or incorrect procedure steps in a skill.
- Clarifying ambiguous language in an agent constraint.
- Correcting a broken link or stale file reference.
- Updating example values that no longer reflect current tooling.
- Fixing CI workflow issues.

---

## Deprecation Policy

### Tokens

When a token is renamed or superseded:

1. Keep the old token in `TOKENS.md` for **one MINOR version** after the replacement is introduced.
2. Mark the old token: `**DEPRECATED as of vX.Y.Z** — use {{NEW_TOKEN_NAME}} instead.`
3. The old token is removed in the following MINOR version.
4. This means adopters have at least one version cycle to migrate.

### Agents

When an agent is removed or merged into another:

1. Replace the agent file content with a stub for **one MINOR version**:
   ```yaml
   ---
   name: "deprecated-agent"
   description: "DEPRECATED as of vX.Y.Z. See [replacement agent] for the replacement."
   status: deprecated
   ---
   ```
2. Log the deprecation in `CHANGELOG.md` under the version it was deprecated.
3. Remove the stub in the following MINOR version.

### Skills

Same policy as agents — stub for one MINOR version, remove in the next.

---

## Support Policy

| Version Type | Support Status |
|--------------|---------------|
| Current MAJOR, latest MINOR | ✅ Actively maintained — bug fixes, security patches, new features |
| Current MAJOR, previous MINOR | 🔧 Security patches only — no new features |
| Previous MAJOR | ❌ Unsupported — upgrade strongly recommended |

### Long-Term Support (LTS)

Designated LTS releases (marked in `CHANGELOG.md`) receive security patches for 12 months after the next MAJOR release. LTS is only declared for MAJOR releases with broad adoption.

---

## Version Locations

The framework version appears in two places. Both must match on every release:

1. `.github/copilot-instructions.md` footer: `## Framework Version` section
2. `framework-manifest.json` root: `"frameworkVersion"` field

Use the release process in `CONTRIBUTING.md` to update both.

---

## Changelog Format

`CHANGELOG.md` uses [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) format:

```markdown
## [vX.Y.Z] — YYYY-MM-DD

### Added
- New skill: `#skill-name` — brief description

### Changed
- Agent: modified behavior, what changed

### Deprecated
- Token `{{OLD_TOKEN}}` — replaced by `{{NEW_TOKEN}}` in next version

### Removed
- Skill `#old-skill` (deprecated in vX.Y-1.0)

### Fixed
- Corrected procedure step N in `#skill-name`

### Security
- Hardened [specific constraint] in [agent/instruction file]
```
