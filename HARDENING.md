<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-setup-atmos/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Verify outputs are available' step in main.yml directly interpolates ${{ steps.atmos.outputs.stdout }}, ${{ steps.atmos.outputs.stderr }}, and ${{ steps.atmos.outputs.exitcode }} inside a run: shell command. Any ${{ ... }} expression interpolated directly into a run: block is a script-injection risk because the value is substituted before the shell parses the command, allowing shell metacharacters to be injected. These should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `.github/workflows/main.yml:51`

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved. Failing references:
- main.yml: actions/checkout@v4, actions/setup-node@v4, EndBug/add-and-commit@v9, cloudposse/github-action-major-release-tagger@v1
- branch.yml: cloudposse/.github/.github/workflows/shared-github-action.yml@main
- release.yml: cloudposse/.github/.github/workflows/shared-release-branches.yml@main
- test-install.yaml: actions/checkout@v4, nick-fields/assert-action@v1 (×4)
- validate-codeowners.yml: actions/checkout@v4, mszostok/codeowners-validator@v0.7.1 (×2)

Locations:

- `.github/workflows/main.yml:9`
- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:57`
- `.github/workflows/main.yml:62`
- `.github/workflows/branch.yml:21`
- `.github/workflows/release.yml:11`
- `.github/workflows/test-install.yaml:21`
- `.github/workflows/test-install.yaml:42`
- `.github/workflows/test-install.yaml:47`
- `.github/workflows/test-install.yaml:52`
- `.github/workflows/test-install.yaml:57`
- `.github/workflows/validate-codeowners.yml:13`
- `.github/workflows/validate-codeowners.yml:16`
- `.github/workflows/validate-codeowners.yml:27`

### missing-permissions (severity: medium)

Three workflow files have no top-level permissions: block and no job-level permissions: blocks on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be broad), and there is no least-privilege enforcement. Each workflow should declare a top-level permissions: block with only the specific scopes required.
- main.yml: no permissions declared for jobs 'build' or 'major-release-tagger'
- test-install.yaml: no permissions declared for jobs 'test' or 'assert'
- validate-codeowners.yml: no permissions declared for job 'validate-codeowners'

Locations:

- `.github/workflows/main.yml:1`
- `.github/workflows/test-install.yaml:1`
- `.github/workflows/validate-codeowners.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three finding types across five workflow files:

1. script-injection (main.yml): Moved ${{ steps.atmos.outputs.stdout/stderr/exitcode }} expressions from the run: shell command into an env: block as ATMOS_STDOUT, ATMOS_STDERR, ATMOS_EXITCODE, then referenced them as plain shell variables.

2. unpinned-uses: Pinned all mutable tag/branch references to full 40-char SHAs with tag comments preserved:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
   - EndBug/add-and-commit@v9 → @a94899bca583c204427a224a7af87c02f9b325d5
   - cloudposse/github-action-major-release-tagger@v1 → @ad2b41fc978968182b88d938c6f51e662eca7700
   - cloudposse/.github/...@main → @8244c7c9142e92281e7841f655fa48e9ceb9b454 (branch.yml and release.yml)
   - nick-fields/assert-action@v1 → @1e012cc9f1bf73ccc96470b56c8887478c647e8a (×4)
   - mszostok/codeowners-validator@v0.7.1 → @2f478ec3b2c8a321d46fca773f6285a447234f6a (×2)

3. missing-permissions: Added top-level permissions blocks:
   - main.yml: contents: write (required for add-and-commit)
   - test-install.yaml: contents: read
   - validate-codeowners.yml: contents: read

