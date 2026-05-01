# Software Bill of Materials (SBOM) Guidance

AI-assisted development introduces a new dependency risk vector: AI agents can add libraries, packages, and transitive dependencies that were not present before the session. This document provides Guardian finding formats for flagging these, SBOM tooling recommendations per ecosystem, and enterprise pipeline integration guidance.

---

## Why AI-Generated Code Requires Special SBOM Attention

AI coding assistants (GitHub Copilot, Claude, Cursor, etc.) frequently:
- Suggest new library imports to solve a problem without flagging them as new dependencies
- Add transitive dependencies through new direct imports without surfacing the full dependency tree
- Introduce dependencies that were current at training-time but have since accumulated CVEs
- Recommend libraries that are popular in training data but unmaintained or low-trust in the current ecosystem

A standard SBOM process captures what is in the manifest. It does not, by default, flag which dependencies were introduced by AI-generated code in a specific session or PR. The Guardian finding format below bridges that gap.

---

## Guardian Finding Format for AI-Introduced Dependencies

When the Guardian perspective identifies a new dependency introduced by AI-generated code in a council review or PR review, use this format:

```
🟠 High | Guardian | AI-Introduced Dependency: <package-name>@<version>

File: <manifest file, e.g., package.json / requirements.txt / go.mod>
Introduced by: AI-generated code in <PR number or session reference>
Ecosystem: <npm / pip / cargo / go / maven / nuget>

Risk Assessment:
- Last published: <date>
- Weekly downloads / popularity signal: <high / medium / low / unknown>
- Maintenance status: <actively maintained / maintenance mode / unmaintained / unknown>
- CVE status at introduction: <none known / CVE-YYYY-NNNNN / check pending>
- License: <SPDX identifier, e.g., MIT, Apache-2.0, GPL-3.0>
- Transitive dependency count introduced: <N>

Recommendation: <Accept / Accept with monitoring / Replace with <alternative> / Remove>
Rationale: <one-sentence justification>
```

Use 🔴 Critical when:
- The dependency has a known HIGH or CRITICAL CVE at the time of introduction
- The license is incompatible with the project's license policy
- The package is unmaintained and has security-relevant functionality (auth, crypto, parsing)

Use 🟠 High when:
- The package is new to the project and introduces >20 transitive dependencies
- The package has not been published in >12 months
- The download/popularity signal is low (potential abandoned or supply-chain-risk package)

Use 🟡 Medium when:
- The dependency is well-established but adds meaningful surface area
- The AI introduced a newer major version with breaking changes from what the project previously used

Use 🔵 Low when:
- The dependency is well-established, actively maintained, no known CVEs, compatible license

### Example Finding

```
🟠 High | Guardian | AI-Introduced Dependency: xml2js@0.6.2

File: package.json
Introduced by: AI-generated code in PR #47 (auth parsing refactor)
Ecosystem: npm

Risk Assessment:
- Last published: 2023-04-14
- Weekly downloads: ~35M (high)
- Maintenance status: maintenance mode (no active feature development)
- CVE status at introduction: CVE-2023-0842 (prototype pollution, CVSS 7.5) — patched in 0.5.0+; version 0.6.2 is not affected, but the package's history warrants monitoring
- License: MIT
- Transitive dependency count introduced: 3

Recommendation: Accept with monitoring
Rationale: No active CVE at this version; flag for quarterly dependency review given historical prototype pollution issues in this package.
```

---

## SBOM Tooling Recommendations by Ecosystem

Generate SBOMs in [CycloneDX](https://cyclonedx.org/) or [SPDX](https://spdx.dev/) format. Both are widely supported by enterprise security platforms.

### npm / Node.js

| Tool | Command | Output Format |
|------|---------|--------------|
| **Syft** (recommended) | `syft dir:. -o cyclonedx-json > sbom.json` | CycloneDX JSON |
| **@cyclonedx/cyclonedx-npm** | `npx @cyclonedx/cyclonedx-npm --output-file sbom.json` | CycloneDX JSON |
| **npm sbom** (built-in, npm ≥9.7) | `npm sbom --sbom-format cyclonedx > sbom.json` | CycloneDX JSON |

Pin the Syft version in CI: check [https://github.com/anchore/syft/releases](https://github.com/anchore/syft/releases) for the latest stable.

### Python / pip

| Tool | Command | Output Format |
|------|---------|--------------|
| **Syft** (recommended) | `syft dir:. -o cyclonedx-json > sbom.json` | CycloneDX JSON |
| **cyclonedx-bom** | `pip install cyclonedx-bom && cyclonedx-py environment -o sbom.json` | CycloneDX JSON |
| **pip-audit** (CVE focus) | `pip-audit --format=cyclonedx-json -o sbom.json` | CycloneDX JSON |

### Rust / Cargo

| Tool | Command | Output Format |
|------|---------|--------------|
| **Syft** (recommended) | `syft dir:. -o cyclonedx-json > sbom.json` | CycloneDX JSON |
| **cargo-cyclonedx** | `cargo install cargo-cyclonedx && cargo cyclonedx` | CycloneDX XML |
| **cargo-audit** (CVE focus) | `cargo audit --json > audit.json` | Custom JSON |

### Go

| Tool | Command | Output Format |
|------|---------|--------------|
| **Syft** (recommended) | `syft dir:. -o cyclonedx-json > sbom.json` | CycloneDX JSON |
| **cyclonedx-gomod** | `go install github.com/CycloneDX/cyclonedx-gomod/cmd/cyclonedx-gomod@latest && cyclonedx-gomod app -output sbom.json` | CycloneDX JSON |

### Syft (Cross-Ecosystem)

[Syft](https://github.com/anchore/syft) by Anchore is the recommended default — it supports all major package ecosystems from a single tool and integrates with [Grype](https://github.com/anchore/grype) for CVE matching.

```bash
# Install (pin the version — never use :latest)
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin v1.21.0

# Generate SBOM
syft dir:. -o cyclonedx-json > sbom.json

# Scan SBOM for CVEs (using Grype)
grype sbom:./sbom.json
```

---

## Enterprise SBOM Pipeline Integration

### When to Generate an SBOM

| Trigger | Action |
|---------|--------|
| PR merges a dependency change (manifest file modified) | Generate SBOM for the merge commit; store as a CI artifact |
| Release / tag | Generate SBOM and attach to the GitHub Release; archive in artifact storage |
| Scheduled weekly scan | Regenerate SBOM for `main` and run Grype CVE scan; alert on new HIGH/CRITICAL findings |
| AI-assisted session introduces new imports | Guardian explicitly calls out each new dependency in the council review finding format above |

### CI Integration Pattern (GitHub Actions)

```yaml
# Add to your quality-gates workflow or a dedicated dependency-review workflow
# Replace SYFT_VERSION with the current pinned version from https://github.com/anchore/syft/releases
- name: Generate SBOM
  uses: anchore/sbom-action@v0   # Pin to a specific SHA for production
  with:
    format: cyclonedx-json
    output-file: sbom.json

- name: Scan for CVEs
  uses: anchore/scan-action@v3   # Pin to a specific SHA for production
  with:
    sbom: sbom.json
    fail-build: true
    severity-cutoff: high        # Fail on HIGH and CRITICAL CVEs
```

> **Pinning note:** `anchore/sbom-action` and `anchore/scan-action` should be pinned to a specific commit SHA for supply-chain security, not just a version tag. Check the [anchore/sbom-action releases](https://github.com/anchore/sbom-action/releases) for the SHA of the version you want to pin.

### Artifact Storage

Store generated SBOMs as:
1. **CI artifacts** — attached to the pipeline run that produced them (ephemeral, for audit trail)
2. **Release assets** — attached to every GitHub Release / deployment tag (permanent, for compliance)
3. **Dependency management platform** — upload to your organization's software composition analysis (SCA) platform (e.g., Snyk, Dependency-Track, Mend/WhiteSource) for continuous CVE monitoring

### Compliance Attestation

For organizations with SSDF, NIST SP 800-218, or Executive Order 14028 compliance requirements:

1. SBOMs must be generated at release time and signed (use [cosign](https://github.com/sigstore/cosign) or your organization's signing infrastructure).
2. The SBOM must capture the full transitive dependency tree, not just direct dependencies.
3. AI-introduced dependencies must be traceable to the PR or session that introduced them — this is why the Guardian finding format includes a `Introduced by:` field.
4. Store signed SBOMs in artifact storage with a retention policy that covers your organization's audit lookback period (commonly 3–7 years).

### Dependency Review Gate (GitHub)

If your project is hosted on GitHub, enable the [Dependency Review Action](https://github.com/actions/dependency-review-action) to block PRs that introduce HIGH/CRITICAL CVEs:

```yaml
- name: Dependency Review
  uses: actions/dependency-review-action@v4   # Pin to SHA for production
  with:
    fail-on-severity: high
    deny-licenses: GPL-3.0, AGPL-3.0   # Adjust to your license policy
```

This is complementary to SBOM generation — it provides immediate PR-level feedback while SBOMs provide the full artifact inventory.
