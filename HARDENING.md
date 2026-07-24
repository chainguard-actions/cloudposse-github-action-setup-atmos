<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos/v3.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-setup-atmos/v3.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use action references pinned to mutable tags or branch names instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved or compromised.

- `.github/workflows/branch.yml` line 25: `uses: cloudposse/.github/.github/workflows/shared-github-action.yml@main`
- `.github/workflows/main.yml` line 13: `uses: actions/checkout@v7`
- `.github/workflows/main.yml` line 19: `uses: actions/setup-node@v6`
- `.github/workflows/main.yml` line 84: `uses: actions/checkout@v7`
- `.github/workflows/main.yml` line 121: `uses: cloudposse/github-action-major-release-tagger@v2`
- `.github/workflows/release.yml` line 13: `uses: cloudposse/.github/.github/workflows/shared-release-branches.yml@main`
- `.github/workflows/test-install.yaml` line 22: `uses: actions/checkout@v7`
- `.github/workflows/test-install.yaml` lines 54, 59, 64, 69: `uses: nick-fields/assert-action@v4`

All references should be pinned to a full 40-character hex commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/branch.yml:25`
- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:84`
- `.github/workflows/main.yml:121`
- `.github/workflows/release.yml:13`
- `.github/workflows/test-install.yaml:22`
- `.github/workflows/test-install.yaml:54`
- `.github/workflows/test-install.yaml:59`
- `.github/workflows/test-install.yaml:64`
- `.github/workflows/test-install.yaml:69`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test-install.yaml` has no top-level `permissions:` key, and neither the `test` job nor the `assert` job defines its own `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Each job should declare the minimal permissions it requires (e.g. `permissions: contents: read`).

Locations:

- `.github/workflows/test-install.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all unpinned action references to full 40-character commit SHAs:
- branch.yml line 25: cloudposse/.github shared-github-action.yml@main → @8244c7c9142e92281e7841f655fa48e9ceb9b454 # main
- main.yml line 13: actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
- main.yml line 19: actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
- main.yml line 84: actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
- main.yml line 121: cloudposse/github-action-major-release-tagger@v2 → @853c309982b8cd78c46a4cdc17cca6720d52541a # v2
- release.yml line 13: cloudposse/.github shared-release-branches.yml@main → @8244c7c9142e92281e7841f655fa48e9ceb9b454 # main
- test-install.yaml line 22: actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1 # v7
- test-install.yaml lines 54,59,64,69: nick-fields/assert-action@v4 → @0efd6166067d9c59d89c710fab4f79fb066f8985 # v4

Added top-level `permissions: contents: read` block to test-install.yaml to fix the missing-permissions finding.

