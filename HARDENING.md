<!-- markdownlint-disable -->

# Hardening Report: julia-actions--setup-julia/v3.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **julia-actions--setup-julia/v3.0.1** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The `finalize-pr-checks` job's `run:` block directly interpolates `${{ needs.checked-in-files.result }}`, `${{ needs.build.result }}`, `${{ needs.npm-run-test.result }}`, `${{ needs.make-targets.result }}`, and `${{ needs.stalecheck-npm-install.result }}` inside shell commands. Per the check rules, ANY `${{ ... }}` expression directly inside a `run:` script is a script-injection finding regardless of which context it reads from, as the value flows through YAML template substitution before the shell processes it.

Locations:

- `.github/workflows/pr_checks.yml:26`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of full 40-character SHA digests, making them vulnerable to supply-chain attacks. Unpinned references: codeql-analysis.yml uses `actions/checkout@v6.0.2`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`.

Locations:

- `.github/workflows/codeql-analysis.yml:36`
- `.github/workflows/codeql-analysis.yml:40`
- `.github/workflows/codeql-analysis.yml:50`
- `.github/workflows/codeql-analysis.yml:63`

### unpinned-uses (severity: high)

Unpinned action references using mutable tags: `actions/checkout@v6.0.2` and `actions/setup-node@v6`.

Locations:

- `.github/workflows/example-builds-defaultarch.yml:24`
- `.github/workflows/example-builds-defaultarch.yml:27`

### unpinned-uses (severity: high)

Unpinned action references using mutable tags: `actions/checkout@v6.0.2` and `actions/setup-node@v6`.

Locations:

- `.github/workflows/example-builds-nightly-defaultarch.yml:28`
- `.github/workflows/example-builds-nightly-defaultarch.yml:31`

### unpinned-uses (severity: high)

Unpinned action references using mutable tags: `actions/checkout@v6.0.2` and `actions/setup-node@v6`.

Locations:

- `.github/workflows/example-builds-nightly.yml:42`
- `.github/workflows/example-builds-nightly.yml:45`

### unpinned-uses (severity: high)

Unpinned action references using mutable tags: `actions/checkout@v6.0.2` (used twice) and `actions/setup-node@v6` (used twice).

Locations:

- `.github/workflows/example-builds.yml:55`
- `.github/workflows/example-builds.yml:58`
- `.github/workflows/example-builds.yml:87`
- `.github/workflows/example-builds.yml:90`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/codeql-analysis.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/example-builds-defaultarch.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/example-builds-nightly-defaultarch.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/example-builds-nightly.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across 5 workflow files:

1. pr_checks.yml (script-injection): Moved ${{ needs.*.result }} expressions from run: block into env: block, referencing them as plain shell variables ($CHECKED_IN_FILES_RESULT, $BUILD_RESULT, etc.).

2. codeql-analysis.yml (unpinned-uses + missing-permissions): Pinned actions/checkout@v6.0.2 → de0fac2e4500dabe0009e67214ff5f5447ce83dd, github/codeql-action/init|autobuild|analyze@v4 → e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81. Added top-level permissions: contents: read, security-events: write.

3. example-builds-defaultarch.yml (unpinned-uses + missing-permissions): Pinned actions/checkout and actions/setup-node to full SHAs. Added permissions: contents: read.

4. example-builds-nightly-defaultarch.yml (unpinned-uses + missing-permissions): Pinned actions/checkout and actions/setup-node to full SHAs. Added permissions: contents: read.

5. example-builds-nightly.yml (unpinned-uses + missing-permissions): Pinned actions/checkout and actions/setup-node to full SHAs. Added permissions: contents: read.

6. example-builds.yml (unpinned-uses): Pinned both occurrences of actions/checkout and actions/setup-node to full SHAs (permissions block already existed).

