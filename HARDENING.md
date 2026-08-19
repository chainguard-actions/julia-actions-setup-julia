<!-- markdownlint-disable -->

# Hardening Report: julia-actions--setup-julia/v2.6.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **julia-actions--setup-julia/v2.6.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

- codeql-analysis.yml: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/autobuild@v3`, `github/codeql-action/analyze@v3`
- example-builds.yml: `actions/checkout@v4`, `actions/setup-node@v4`
- example-builds-defaultarch.yml: `actions/checkout@v4`, `actions/setup-node@v4`
- example-builds-nightly.yml: `actions/checkout@v4`, `actions/setup-node@v4`
- example-builds-nightly-defaultarch.yml: `actions/checkout@v4`, `actions/setup-node@v4`

Locations:

- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/codeql-analysis.yml:47`
- `.github/workflows/codeql-analysis.yml:56`
- `.github/workflows/codeql-analysis.yml:65`
- `.github/workflows/example-builds.yml:43`
- `.github/workflows/example-builds.yml:45`
- `.github/workflows/example-builds-defaultarch.yml:36`
- `.github/workflows/example-builds-defaultarch.yml:38`
- `.github/workflows/example-builds-nightly.yml:40`
- `.github/workflows/example-builds-nightly.yml:42`
- `.github/workflows/example-builds-nightly-defaultarch.yml:38`
- `.github/workflows/example-builds-nightly-defaultarch.yml:40`

### script-injection (severity: high)

Sub-rule (a): In pr_checks.yml, the `finalize-pr-checks` job's `run:` block directly interpolates `${{ needs.*.result }}` expressions into shell commands. Although `needs.*.result` values are GitHub-controlled (success/failure/cancelled/skipped), any `${{ ... }}` expression interpolated directly inside a `run:` block is a script-injection risk because the value is substituted into the shell script before the shell parses it. Offending lines:
  `echo checked-in-files: ${{ needs.checked-in-files.result }}`
  `echo build: ${{ needs.build.result }}`
  `echo npm-run-test: ${{ needs.npm-run-test.result }}`
  `echo make-targets: ${{ needs.make-targets.result }}`
  `echo stalecheck-npm-install: ${{ needs.stalecheck-npm-install.result }}`

Locations:

- `.github/workflows/pr_checks.yml:26`
- `.github/workflows/pr_checks.yml:27`
- `.github/workflows/pr_checks.yml:28`
- `.github/workflows/pr_checks.yml:29`
- `.github/workflows/pr_checks.yml:30`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks on any job. Without explicit permissions, workflows inherit the repository's default token permissions (which may be write-all), granting unnecessarily broad access.

- codeql-analysis.yml
- example-builds.yml
- example-builds-defaultarch.yml
- example-builds-nightly.yml
- example-builds-nightly-defaultarch.yml

Locations:

- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/example-builds.yml:1`
- `.github/workflows/example-builds-defaultarch.yml:1`
- `.github/workflows/example-builds-nightly.yml:1`
- `.github/workflows/example-builds-nightly-defaultarch.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across 6 workflow files:

1. unpinned-uses: Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262, actions/setup-node@v4 to SHA 49933ea5288caeca8642d1e84afbd3f7d6820020, and all three github/codeql-action/* @v3 references to SHA b7351df727350dca84cb9d725d57dcf5bc82ba26 across codeql-analysis.yml, example-builds.yml, example-builds-defaultarch.yml, example-builds-nightly.yml, and example-builds-nightly-defaultarch.yml.

2. missing-permissions: Added top-level permissions blocks to all 5 affected workflow files. codeql-analysis.yml gets actions:read, contents:read, security-events:write (required for CodeQL). The four example-builds files get contents:read only.

3. script-injection: In pr_checks.yml finalize-pr-checks job, moved all five ${{ needs.*.result }} expressions from the run: block into an env: block with named variables (CHECKED_IN_FILES_RESULT, BUILD_RESULT, NPM_RUN_TEST_RESULT, MAKE_TARGETS_RESULT, STALECHECK_NPM_INSTALL_RESULT), and updated the shell script to reference them as plain env vars.

