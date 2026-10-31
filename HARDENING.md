<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.9.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the composite step 'release', values the user can control are written to $GITHUB_PATH without the required `printf '%s' ... | tr -d '\n\r'` step to strip newlines. (1) `echo "$install_dir" >> "$GITHUB_PATH"`: install_dir comes straight from inputs.install_dir. (2) `install="$RUNNER_TOOL_CACHE/cr/$version/$(uname -m)"` followed by `echo "$install" >> "$GITHUB_PATH"`: the path includes inputs.version. A value containing a newline can add extra entries to PATH. Fix: clean each value first, e.g. `safe=$(printf '%s' "$install_dir" | tr -d '\n\r'); echo "$safe" >> "$GITHUB_PATH"`, and do the same for $install.

Locations:

- `action.yml:124`
- `action.yml:127`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

In action.yml (release step), $install and $install_dir now have \n and \r removed with printf '%s' | tr -d '\n\r' before they are written to $GITHUB_PATH. The rest of the script is unchanged.

