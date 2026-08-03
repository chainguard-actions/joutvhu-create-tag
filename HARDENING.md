<!-- markdownlint-disable -->

# Hardening Report: joutvhu--create-tag/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **joutvhu--create-tag/v1.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Workflow files reference external actions using mutable tags instead of full 40-character commit SHAs. In auto-build.yml: `actions/checkout@v4` (line 15) and `actions/setup-node@v4` (line 17). In update-tag.yml: `actions/checkout@v4` (line 13) and `joutvhu/get-release@v1` (line 17). Mutable tags can be moved to point to different (potentially malicious) commits, enabling supply-chain attacks.

Locations:

- `.github/workflows/auto-build.yml:15`
- `.github/workflows/auto-build.yml:17`
- `.github/workflows/update-tag.yml:13`
- `.github/workflows/update-tag.yml:17`

### script-injection (severity: high)

Sub-rule (a) violation: A `${{ ... }}` expression is directly interpolated inside a `run:` shell command string in update-tag.yml. Line 24 contains: `echo "::set-output name=version::$(echo ${{ steps.current_release.outputs.tag_name }} | cut -f 1 -d .)"`. The `steps.current_release.outputs.tag_name` value flows through YAML template substitution before the shell parses it, allowing an attacker who controls the release tag name to inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the shell script.

Locations:

- `.github/workflows/update-tag.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all four unpinned action references by resolving their full commit SHAs via lookup_action_sha and updating the uses: lines with the format 'owner/repo@SHA # tag'. Fixed the script injection in update-tag.yml by moving the ${{ steps.current_release.outputs.tag_name }} expression into an env: variable (TAG_NAME) and referencing it as "$TAG_NAME" in the shell script, preventing attacker-controlled tag names from being interpreted as shell commands.

