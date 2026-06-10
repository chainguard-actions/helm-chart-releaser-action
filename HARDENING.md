<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **helm--chart-releaser-action/v1.7.0** was hardened automatically. 25 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block in action.yml directly interpolates `${{ inputs.* }}` expressions throughout the shell script (sub-rule a). YAML template substitution occurs before the shell processes the string, so an attacker-controlled input value can inject shell metacharacters and execute arbitrary commands. Affected lines include: `args+=(--charts-dir "${{ inputs.charts_dir }}")`, `if [[ -n "${{ inputs.version }}" ]]`, `args+=(--version "${{ inputs.version }}")`, `if [[ -n "${{ inputs.config }}" ]]`, `args+=(--config "${{ inputs.config }}")`, `if [[ -z "${{ inputs.install_dir }}" ]]`, `install="$RUNNER_TOOL_CACHE/cr/${{ inputs.version }}/$(uname -m)"`, `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"` (also unquoted — sub-rule b), and all remaining `${{ inputs.install_only }}`, `${{ inputs.skip_packaging }}`, `${{ inputs.skip_existing }}`, `${{ inputs.skip_upload }}`, `${{ inputs.mark_as_latest }}`, `${{ inputs.packages_with_index }}`, `${{ inputs.pages_branch }}` interpolations. All inputs should be passed via an `env:` block and referenced as double-quoted shell variables (e.g., `"$INPUT_VERSION"`).

Locations:

- `action.yml:70`

### github-env-injection (severity: high)

The `run:` block writes `${{ inputs.install_dir }}` directly to `$GITHUB_PATH` without quoting or sanitization: `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`. This is both unquoted (shell word-splitting/glob expansion) and unsanitized (no `printf '%s' ... | tr -d '\n\r'` step), allowing an attacker to inject newlines into `$GITHUB_PATH` and potentially prepend malicious directories to the runner's PATH. The fix is to route the value through an `env:` variable and apply the sanitization pipeline before writing.

Locations:

- `action.yml:87`

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

Fixed all 25 findings in action.yml by: (1) Adding an env: block to the release step that maps all 11 inputs to named environment variables (INPUT_VERSION, INPUT_CONFIG, INPUT_CHARTS_DIR, INPUT_INSTALL_DIR, INPUT_INSTALL_ONLY, INPUT_SKIP_PACKAGING, INPUT_SKIP_EXISTING, INPUT_SKIP_UPLOAD, INPUT_MARK_AS_LATEST, INPUT_PACKAGES_WITH_INDEX, INPUT_PAGES_BRANCH). (2) Replacing all ${{ inputs.* }} inline expressions in the run: block with the corresponding $INPUT_* shell variables. (3) Sanitizing the GITHUB_PATH write for install_dir by using `safe_install_dir=$(printf '%s' "$INPUT_INSTALL_DIR" | tr -d '\n\r')` before writing to $GITHUB_PATH, preventing newline injection attacks.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed three github-env-injection vulnerabilities in action.yml:
1. GITHUB_PATH injection (line 83): The `install` variable (containing user-controlled `$INPUT_VERSION`) is now sanitized with `printf '%s' "$install" | tr -d '\n\r'` before being written to $GITHUB_PATH, matching the existing sanitization pattern used in the INPUT_INSTALL_DIR branch.
2. GITHUB_OUTPUT injection via changed_charts.txt (line 113): Replaced bare `cat changed_charts.txt >> "$GITHUB_OUTPUT"` with a while-read loop that sanitizes each line using `printf '%s' "$line" | tr -d '\n\r'` before writing.
3. GITHUB_OUTPUT injection via chart_version.txt (line 117): Applied the same sanitizing while-read loop pattern to chart_version.txt.

