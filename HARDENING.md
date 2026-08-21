<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.7.0** was hardened automatically. 25 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.* }}` expressions inside shell command strings throughout the entire step. Before the shell ever sees the script, GitHub Actions substitutes these values verbatim, allowing an attacker-controlled input to inject arbitrary shell commands. Affected lines include:
- Line 77: `args+=(--charts-dir "${{ inputs.charts_dir }}")`
- Line 79: `if [[ -n "${{ inputs.version }}" ]]; then`
- Line 80: `args+=(--version "${{ inputs.version }}")`
- Line 83: `if [[ -n "${{ inputs.config }}" ]]; then`
- Line 84: `args+=(--config "${{ inputs.config }}")`
- Line 87: `if [[ -z "${{ inputs.install_dir }}" ]]; then`
- Line 88: `install="$RUNNER_TOOL_CACHE/cr/${{ inputs.version }}/$(uname -m)"`
- Line 92: `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`
- Line 93: `args+=(--install-dir "${{ inputs.install_dir }}")`
- And all subsequent `${{ inputs.* }}` checks (install_only, skip_packaging, skip_existing, skip_upload, mark_as_latest, packages_with_index, pages_branch).
All inputs should be passed via `env:` variables and referenced as `"$VAR"` in the shell script instead.

Locations:

- `action.yml:77`
- `action.yml:79`
- `action.yml:80`
- `action.yml:83`
- `action.yml:84`
- `action.yml:87`
- `action.yml:88`
- `action.yml:92`
- `action.yml:93`

### github-env-injection (severity: high)

The `run:` block writes `${{ inputs.install_dir }}` directly to `$GITHUB_PATH` without sanitization. The offending line is: `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`. This is a direct write of an attacker-controlled input value to a special GitHub environment file with no `printf '%s' ... | tr -d '\n\r'` sanitization step applied before the write. A malicious value containing newlines could inject additional entries into `$GITHUB_PATH`, potentially hijacking PATH lookups for subsequent steps. The fix is to route the value through an `env:` variable and sanitize it: `safe=$(printf '%s' "$INSTALL_DIR" | tr -d '\n\r'); echo "$safe" >> "$GITHUB_PATH"`.

Locations:

- `action.yml:92`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.charts_dir }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:76`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:78`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:79`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.config }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:82`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.config }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:83`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.install_dir }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:86`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:87`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.install_dir }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:91`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.install_dir }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:92`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.install_only }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:95`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.install_only }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:96`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_packaging }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:99`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_packaging }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:100`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_existing }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:103`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_existing }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:104`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_upload }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:107`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.skip_upload }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:108`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.mark_as_latest }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:111`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.mark_as_latest }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:112`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.packages_with_index }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:115`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.packages_with_index }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:116`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pages_branch }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:119`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pages_branch }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:120`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Moved all ${{ inputs.* }} expressions from the run: shell block into a step-level env: block. Each input is now mapped to an INPUT_* environment variable (INPUT_VERSION, INPUT_CONFIG, INPUT_CHARTS_DIR, INPUT_INSTALL_DIR, INPUT_INSTALL_ONLY, INPUT_SKIP_PACKAGING, INPUT_SKIP_EXISTING, INPUT_SKIP_UPLOAD, INPUT_MARK_AS_LATEST, INPUT_PACKAGES_WITH_INDEX, INPUT_PAGES_BRANCH). The shell script references these as plain $INPUT_* variables. Additionally, the install_dir write to $GITHUB_PATH is now sanitized with `printf '%s' "$INPUT_INSTALL_DIR" | tr -d '\n\r'` before writing, preventing newline injection attacks.

### Iteration 2

**Fixes applied:** github-env-injection, hardcoded-credentials

**Notes:**

1. action.yml (line ~91): Added sanitization for the `install` variable in the `if [[ -z "$INPUT_INSTALL_DIR" ]]` branch. The variable now goes through `printf '%s' "$install" | tr -d '\n\r'` before being written to $GITHUB_PATH, preventing newline injection from a caller-controlled `inputs.version` value. 2. .github/workflows/test-action.yml (lines 24 and 44): Replaced both instances of `CR_TOKEN: "FAKE_SECRETS"` with `CR_TOKEN: ${{ secrets.CR_TOKEN }}` to use a proper GitHub Actions secret reference instead of a hardcoded literal credential.

