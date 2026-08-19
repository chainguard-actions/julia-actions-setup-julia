<!-- markdownlint-disable -->

# Hardening Report: julia-actions--setup-julia/v3.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **julia-actions--setup-julia/v3.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

- codeql-analysis.yml: `actions/checkout@v6.0.2`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- example-builds-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly-defaultarch.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds-nightly.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`
- example-builds.yml: `actions/checkout@v6.0.2`, `actions/setup-node@v6`

Locations:

- `.github/workflows/codeql-analysis.yml:35`
- `.github/workflows/codeql-analysis.yml:40`
- `.github/workflows/codeql-analysis.yml:47`
- `.github/workflows/codeql-analysis.yml:58`
- `.github/workflows/example-builds-defaultarch.yml:25`
- `.github/workflows/example-builds-defaultarch.yml:27`
- `.github/workflows/example-builds-nightly-defaultarch.yml:26`
- `.github/workflows/example-builds-nightly-defaultarch.yml:28`
- `.github/workflows/example-builds-nightly.yml:38`
- `.github/workflows/example-builds-nightly.yml:40`
- `.github/workflows/example-builds.yml:52`
- `.github/workflows/example-builds.yml:54`
- `.github/workflows/example-builds.yml:94`
- `.github/workflows/example-builds.yml:96`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating the principle of least privilege.

- codeql-analysis.yml: no permissions block at top level or job level
- example-builds-defaultarch.yml: no permissions block at top level or job level
- example-builds-nightly-defaultarch.yml: no permissions block at top level or job level
- example-builds-nightly.yml: no permissions block at top level or job level

Locations:

- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/example-builds-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly.yml:1`

### script-injection (severity: high)

In pr_checks.yml, the `finalize-pr-checks` job's first `run:` step directly interpolates `${{ needs.*.result }}` expressions inside shell commands. Per rule (a), ANY `${{ ... }}` expression interpolated directly inside a `run:` block is a script-injection finding, as the value flows through YAML template substitution before the shell parses it. Offending lines:
  `echo checked-in-files: ${{ needs.checked-in-files.result }}`
  `echo build: ${{ needs.build.result }}`
  `echo npm-run-test: ${{ needs.npm-run-test.result }}`
  `echo make-targets: ${{ needs.make-targets.result }}`
  `echo stalecheck-npm-install: ${{ needs.stalecheck-npm-install.result }}`

Locations:

- `.github/workflows/pr_checks.yml:27`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across 6 workflow files:

1. unpinned-uses: Pinned all mutable tag references to full 40-char SHAs:
   - actions/checkout@v6.0.2 → @de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - github/codeql-action/init@v4 → @7188fc363630916deb702c7fdcf4e481b751f97a # v4
   - github/codeql-action/autobuild@v4 → @7188fc363630916deb702c7fdcf4e481b751f97a # v4
   - github/codeql-action/analyze@v4 → @7188fc363630916deb702c7fdcf4e481b751f97a # v4

2. missing-permissions: Added top-level permissions blocks to:
   - codeql-analysis.yml: contents: read, security-events: write (CodeQL needs security-events: write)
   - example-builds-defaultarch.yml: contents: read
   - example-builds-nightly-defaultarch.yml: contents: read
   - example-builds-nightly.yml: contents: read

3. script-injection: In pr_checks.yml finalize-pr-checks job, moved all ${{ needs.*.result }} expressions into an env: block and referenced them as plain shell environment variables in the run: block.

