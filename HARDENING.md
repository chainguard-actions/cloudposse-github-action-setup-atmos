<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-setup-atmos/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions and reusable workflows using mutable tags or branch names instead of full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved or overwritten.

.github/workflows/main.yml:
  - uses: actions/checkout@v7 (lines 13 and 63)
  - uses: actions/setup-node@v6 (line 19)
  - uses: cloudposse/github-action-major-release-tagger@v2 (line 80)

.github/workflows/branch.yml:
  - uses: cloudposse/.github/.github/workflows/shared-github-action.yml@main (line 18)

.github/workflows/release.yml:
  - uses: cloudposse/.github/.github/workflows/shared-release-branches.yml@main (line 8)

.github/workflows/test-install.yaml:
  - uses: actions/checkout@v7 (line 22)
  - uses: nick-fields/assert-action@v4 (lines 50, 55, 60, 65)

Locations:

- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:63`
- `.github/workflows/main.yml:80`
- `.github/workflows/branch.yml:18`
- `.github/workflows/release.yml:8`
- `.github/workflows/test-install.yaml:22`
- `.github/workflows/test-install.yaml:50`
- `.github/workflows/test-install.yaml:55`
- `.github/workflows/test-install.yaml:60`
- `.github/workflows/test-install.yaml:65`

### missing-permissions (severity: medium)

The workflow file test-install.yaml has no top-level `permissions:` key and neither of its two jobs (`test` and `assert`) defines a `permissions:` block. Without explicit permissions, the workflow inherits the default repository permissions, which may be broader than necessary (e.g., write access to contents). Explicit minimal permissions should be declared at the top level or on each job.

Locations:

- `.github/workflows/test-install.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by replacing mutable tags/branches with full 40-character commit SHAs (preserving original tags as comments): actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38, cloudposse/github-action-major-release-tagger@v2 → @853c309982b8cd78c46a4cdc17cca6720d52541a, cloudposse/.github@main → @8244c7c9142e92281e7841f655fa48e9ceb9b454 (used for both branch.yml and release.yml), nick-fields/assert-action@v4 → @0efd6166067d9c59d89c710fab4f79fb066f8985 (all 4 occurrences in test-install.yaml). Added top-level `permissions: contents: read` to test-install.yaml to fix the missing-permissions finding.

