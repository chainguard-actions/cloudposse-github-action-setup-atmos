<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos/v2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-setup-atmos/v2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Three ${{ ... }} expressions are directly interpolated inside a run: shell command string in the 'Verify outputs are available' step. The values `${{ steps.atmos.outputs.stdout }}`, `${{ steps.atmos.outputs.stderr }}`, and `${{ steps.atmos.outputs.exitcode }}` are substituted into the shell command before the shell executes it, allowing any attacker-controlled content in those step outputs to inject arbitrary shell commands.

Locations:

- `.github/workflows/main.yml:51`
- `.github/workflows/main.yml:52`
- `.github/workflows/main.yml:53`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions and reusable workflows using mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

.github/workflows/main.yml:
  - uses: actions/checkout@v4 (line 9)
  - uses: actions/setup-node@v4 (line 14)
  - uses: EndBug/add-and-commit@v9 (line 57)
  - uses: cloudposse/github-action-major-release-tagger@v1 (line 62)

.github/workflows/branch.yml:
  - uses: cloudposse/.github/.github/workflows/shared-github-action.yml@main (line 21)

.github/workflows/release.yml:
  - uses: cloudposse/.github/.github/workflows/shared-release-branches.yml@main (line 10)

.github/workflows/test-install.yaml:
  - uses: actions/checkout@v4 (line 22)
  - uses: nick-fields/assert-action@v1 (lines 49, 54, 59, 64)

.github/workflows/validate-codeowners.yml:
  - uses: actions/checkout@v4 (line 11)
  - uses: mszostok/codeowners-validator@v0.7.1 (lines 14, 22)

Locations:

- `.github/workflows/main.yml:9`
- `.github/workflows/branch.yml:21`
- `.github/workflows/release.yml:10`
- `.github/workflows/test-install.yaml:22`
- `.github/workflows/validate-codeowners.yml:11`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (often write-all for older repos), granting jobs more access than necessary.

- .github/workflows/main.yml: no top-level permissions; jobs 'build' and 'major-release-tagger' have no job-level permissions.
- .github/workflows/test-install.yaml: no top-level permissions; jobs 'test' and 'assert' have no job-level permissions.
- .github/workflows/validate-codeowners.yml: no top-level permissions; job 'validate-codeowners' has no job-level permissions.

Locations:

- `.github/workflows/main.yml:1`
- `.github/workflows/test-install.yaml:1`
- `.github/workflows/validate-codeowners.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across five workflow files:

1. script-injection (main.yml lines 51-53): Moved ${{ steps.atmos.outputs.stdout/stderr/exitcode }} expressions from the 'Verify outputs are available' run: shell string into the step's env: block as ATMOS_STDOUT, ATMOS_STDERR, ATMOS_EXITCODE. Shell script now references plain env vars.

2. unpinned-uses: Pinned all mutable tag/branch references to full 40-char SHAs with tag comments:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
   - EndBug/add-and-commit@v9 → @a94899bca583c204427a224a7af87c02f9b325d5
   - cloudposse/github-action-major-release-tagger@v1 → @ad2b41fc978968182b88d938c6f51e662eca7700
   - cloudposse/.github shared workflows @main → @3911c663309ecdda30d8b8fcbec7bde19d1d6ddb (both branch.yml and release.yml)
   - nick-fields/assert-action@v1 → @1e012cc9f1bf73ccc96470b56c8887478c647e8a (4 occurrences)
   - mszostok/codeowners-validator@v0.7.1 → @2f478ec3b2c8a321d46fca773f6285a447234f6a (2 occurrences)

3. missing-permissions: Added top-level and job-level permissions blocks to main.yml (contents: write), test-install.yaml (contents: read), and validate-codeowners.yml (contents: read).

