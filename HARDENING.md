<!-- markdownlint-disable -->

# Hardening Report: julia-actions--setup-julia/v2.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **julia-actions--setup-julia/v2.7.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions by mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

- codeql-analysis.yml: `actions/checkout@v6.0.2`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- example-builds.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`

All of these should be pinned to their full 40-character hex commit SHA (e.g. `actions/checkout@<sha> # v6.0.2`).

Locations:

- `.github/workflows/codeql-analysis.yml:44`
- `.github/workflows/codeql-analysis.yml:49`
- `.github/workflows/codeql-analysis.yml:58`
- `.github/workflows/codeql-analysis.yml:68`
- `.github/workflows/example-builds.yml:38`
- `.github/workflows/example-builds.yml:41`
- `.github/workflows/example-builds-defaultarch.yml:36`
- `.github/workflows/example-builds-defaultarch.yml:39`
- `.github/workflows/example-builds-nightly.yml:37`
- `.github/workflows/example-builds-nightly.yml:40`
- `.github/workflows/example-builds-nightly-defaultarch.yml:37`
- `.github/workflows/example-builds-nightly-defaultarch.yml:40`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be broad). Each file should declare the minimal required permissions.

- codeql-analysis.yml: no permissions declared at top level or job level
- example-builds.yml: no permissions declared at top level or job level
- example-builds-defaultarch.yml: no permissions declared at top level or job level
- example-builds-nightly.yml: no permissions declared at top level or job level
- example-builds-nightly-defaultarch.yml: no permissions declared at top level or job level

Locations:

- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/example-builds.yml:1`
- `.github/workflows/example-builds-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly.yml:1`
- `.github/workflows/example-builds-nightly-defaultarch.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 5 workflow files: (1) Pinned all action references to full 40-char SHAs: actions/checkout@v6.0.2→de0fac2e..., actions/setup-node@v6→249970729..., github/codeql-action/{init,autobuild,analyze}@v4→7188fc36... (2) Added top-level permissions blocks: codeql-analysis.yml gets 'contents: read' + 'security-events: write' (required for CodeQL to upload SARIF results); all four example-builds workflows get 'contents: read'.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

In `.github/workflows/pr_checks.yml`, the `finalize-pr-checks` job's first step had five `${{ needs.*.result }}` expressions directly interpolated into a `run:` shell block. Fixed by adding an `env:` block to the step with named variables (CHECKED_IN_FILES_RESULT, BUILD_RESULT, NPM_RUN_TEST_RESULT, MAKE_TARGETS_RESULT, STALECHECK_NPM_INSTALL_RESULT) and updating the `run:` script to reference those environment variables double-quoted instead.

