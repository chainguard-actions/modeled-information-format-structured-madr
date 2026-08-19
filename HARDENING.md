<!-- markdownlint-disable -->

# Hardening Report: zircote--structured-madr/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **zircote--structured-madr/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files and action.yml reference actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks.

- action.yml: `uses: actions/setup-node@v6` (tag)
- validate-adrs.yml: `uses: actions/checkout@v6` (tag, in both jobs)
- deploy.yml: `uses: actions/checkout@v6` (tag), `uses: withastro/action@v6` (tag), `uses: actions/deploy-pages@v5` (tag)
- dependabot-automerge.yml: `uses: zircote/.github/.github/workflows/reusable-dependabot-automerge.yml@main` (branch)

Locations:

- `action.yml:54`
- `.github/workflows/validate-adrs.yml:21`
- `.github/workflows/validate-adrs.yml:34`
- `.github/workflows/deploy.yml:18`
- `.github/workflows/deploy.yml:19`
- `.github/workflows/deploy.yml:26`
- `.github/workflows/dependabot-automerge.yml:16`

### missing-permissions (severity: medium)

The workflow file validate-adrs.yml has no top-level `permissions:` key and neither of its jobs (`validate-project-adrs`, `validate-examples`) defines a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default repository token permissions, which may be overly broad.

Locations:

- `.github/workflows/validate-adrs.yml:1`

### script-injection (severity: high)

Sub-rule (a) violation: The `Configure Git credentials` step in daily-docs-review.lock.yml directly interpolates `${{ github.token }}` inside a `run:` shell command string. Any `${{ ... }}` expression interpolated directly into a shell script is a script-injection risk because the value is substituted before the shell parses the command. The offending line is:
  `git remote set-url origin "https://x-access-token:${{ github.token }}@${SERVER_URL_STRIPPED}/${REPO_NAME}.git"`
This pattern appears twice in the file (in the `agent` job and in the `safe_outputs` job). The token should instead be passed via an `env:` variable and referenced as `$GIT_TOKEN` in the shell script.

Locations:

- `.github/workflows/daily-docs-review.lock.yml:131`
- `.github/workflows/daily-docs-review.lock.yml:346`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings:

1. unpinned-uses: Pinned all mutable tag/branch references to full 40-char commit SHAs:
   - action.yml: actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - validate-adrs.yml (2x): actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6
   - deploy.yml: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6, withastro/action@v6 → @e84f40bd8d2caa9e768ec82ad30dd81f0b280853 # v6, actions/deploy-pages@v5 → @cd2ce8fcbc39b97be8ca5fce6e763baed58fa128 # v5
   - dependabot-automerge.yml: reusable-dependabot-automerge.yml@main → @b2c87d254e899550e543bedea186335b48b9a41e # main

2. missing-permissions: Added top-level 'permissions: {}' to validate-adrs.yml plus job-level 'permissions: { contents: read }' for both jobs.

3. script-injection: Both occurrences of ${{ github.token }} directly in run: scripts in daily-docs-review.lock.yml moved to env: blocks as GIT_TOKEN, referenced as ${GIT_TOKEN} in the shell scripts.

