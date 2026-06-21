<!-- markdownlint-disable -->

# Hardening Report: zircote--structured-madr/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **zircote--structured-madr/v1.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml references `actions/setup-node@v6`, which uses a mutable version tag instead of a pinned 40-character commit SHA. This means the action could silently pull in changed or malicious code if the tag is moved. It should be pinned to a full SHA, e.g. `actions/setup-node@<40-char-sha> # v6`.

Locations:

- `action.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `actions/setup-node@v6` to full commit SHA `actions/setup-node@48b55a011bda9f5d6aeb4c2d9c7362e8dae4041e # v6` in action.yml line 52. SHA was resolved via lookup_action_sha for the v6 tag.

