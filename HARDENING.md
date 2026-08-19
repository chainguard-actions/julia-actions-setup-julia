<!-- markdownlint-disable -->

# Hardening Report: julia-actions--setup-julia/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **julia-actions--setup-julia/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

- codeql-analysis.yml: `actions/checkout@v6.0.2`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- example-builds-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`

Locations:

- `.github/workflows/codeql-analysis.yml:44`
- `.github/workflows/codeql-analysis.yml:48`
- `.github/workflows/codeql-analysis.yml:56`
- `.github/workflows/codeql-analysis.yml:66`
- `.github/workflows/example-builds-defaultarch.yml:24`
- `.github/workflows/example-builds-defaultarch.yml:26`
- `.github/workflows/example-builds-nightly-defaultarch.yml:27`
- `.github/workflows/example-builds-nightly-defaultarch.yml:29`
- `.github/workflows/example-builds-nightly.yml:37`
- `.github/workflows/example-builds-nightly.yml:39`
- `.github/workflows/example-builds.yml:54`
- `.github/workflows/example-builds.yml:57`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, workflows inherit the default repository permissions (which may be write-all), granting unnecessarily broad access to the GITHUB_TOKEN.

- codeql-analysis.yml: no permissions at top-level or job level
- example-builds-defaultarch.yml: no permissions at top-level or job level
- example-builds-nightly-defaultarch.yml: no permissions at top-level or job level
- example-builds-nightly.yml: no permissions at top-level or job level

Locations:

- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/example-builds-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all 12 unpinned action references to full 40-character commit SHAs across 5 workflow files: actions/checkout@v6.0.2→SHA de0fac2e, actions/setup-node@v6→SHA 249970729c, github/codeql-action/{init,autobuild,analyze}@v4→SHA 7188fc36. Added top-level permissions blocks to 4 workflow files that lacked them: codeql-analysis.yml gets 'contents: read' + 'security-events: write' (required for CodeQL to upload SARIF results); example-builds-defaultarch.yml, example-builds-nightly-defaultarch.yml, and example-builds-nightly.yml each get 'contents: read'. example-builds.yml already had a permissions block so only action pinning was needed there.

