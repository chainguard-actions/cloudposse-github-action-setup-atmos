<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos--/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cloudposse--github-action-setup-atmos--/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of pinned full-length SHA digests, making them vulnerable to supply-chain attacks. Unpinned references found:
- branch.yml: `cloudposse/.github/.github/workflows/shared-github-action.yml@main`
- main.yml: `actions/checkout@v7`, `actions/setup-node@v6`, `actions/checkout@v7` (integration job), `cloudposse/github-action-major-release-tagger@v2`
- release.yml: `cloudposse/.github/.github/workflows/shared-release-branches.yml@main`
- test-install.yaml: `actions/checkout@v7`, `nick-fields/assert-action@v4` (×4)

Locations:

- `.github/workflows/branch.yml:24`
- `.github/workflows/main.yml:12`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:66`
- `.github/workflows/main.yml:97`
- `.github/workflows/release.yml:13`
- `.github/workflows/test-install.yaml:21`
- `.github/workflows/test-install.yaml:50`
- `.github/workflows/test-install.yaml:55`
- `.github/workflows/test-install.yaml:60`
- `.github/workflows/test-install.yaml:65`

### missing-permissions (severity: medium)

The workflow file test-install.yaml has no top-level `permissions:` key and neither of its jobs (`test`, `assert`) defines a `permissions:` block. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/test-install.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all mutable action references to full commit SHAs with tag comments: actions/checkout@v7→9c091bb, actions/setup-node@v6→48b55a0, cloudposse/github-action-major-release-tagger@v2→853c309, nick-fields/assert-action@v4→0efd616, cloudposse/.github@main→8244c7c. Added top-level `permissions: contents: read` to test-install.yaml to address missing-permissions finding.

