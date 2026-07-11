<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-setup-atmos/v3.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cloudposse--github-action-setup-atmos/v3.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tag or branch refs instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks.

.github/workflows/branch.yml:
- uses: cloudposse/.github/.github/workflows/shared-github-action.yml@main (branch ref)

.github/workflows/main.yml:
- uses: actions/checkout@v7 (tag ref, appears in build and integration jobs)
- uses: actions/setup-node@v6 (tag ref)
- uses: cloudposse/github-action-major-release-tagger@v2 (tag ref)

.github/workflows/release.yml:
- uses: cloudposse/.github/.github/workflows/shared-release-branches.yml@main (branch ref)

.github/workflows/test-install.yaml:
- uses: actions/checkout@v7 (tag ref)
- uses: nick-fields/assert-action@v4 (tag ref, used in 4 steps)

Locations:

- `.github/workflows/branch.yml:18`
- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:68`
- `.github/workflows/main.yml:107`
- `.github/workflows/release.yml:9`
- `.github/workflows/test-install.yaml:20`
- `.github/workflows/test-install.yaml:47`
- `.github/workflows/test-install.yaml:51`
- `.github/workflows/test-install.yaml:56`
- `.github/workflows/test-install.yaml:61`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test-install.yaml has no top-level 'permissions:' key and neither the 'test' job nor the 'assert' job defines a job-level 'permissions:' block. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g. write access to contents).

Locations:

- `.github/workflows/test-install.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all mutable action refs to full 40-char SHAs: actions/checkout@v7→9c091bb, actions/setup-node@v6→48b55a0, cloudposse/github-action-major-release-tagger@v2→853c309, nick-fields/assert-action@v4→0efd616 (4 occurrences), cloudposse/.github@main→8244c7c (branch.yml and release.yml). Added top-level 'permissions: {}' to test-install.yaml to prevent inheriting overly broad default permissions.

