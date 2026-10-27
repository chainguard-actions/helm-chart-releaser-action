<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.8.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The composite step 'release' writes values that come from user inputs to $GITHUB_PATH without first running them through printf '%s' ... | tr -d '\n\r'. Line 119 writes `echo "$install_dir" >> "$GITHUB_PATH"`, where install_dir comes from inputs.install_dir. Line 116 writes `echo "$install" >> "$GITHUB_PATH"`, where install is built from $version, which comes from inputs.version. If either value contains a newline, an attacker can add extra entries to PATH.

Locations:

- `action.yml:116`
- `action.yml:119`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

In action.yml, the 'release' step now strips CR and LF from both values before writing them to $GITHUB_PATH. It does this with printf '%s' | tr -d '\n\r' and then writes the result with printf '%s\n'. The two values are $install (built from inputs.version) and $install_dir (from inputs.install_dir). The --install-dir argument passed to cr.sh is unchanged, so normal values behave as before. Nothing was run to test this.

