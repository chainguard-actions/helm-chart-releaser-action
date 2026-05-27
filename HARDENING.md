# Hardening Report: helm--chart-releaser-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **helm--chart-releaser-action/v1.7.0** was hardened automatically. 25 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple attacker-controlled `inputs.*` expressions are interpolated directly inside the `run:` shell block in action.yml without first being assigned to environment variables. An attacker who controls these inputs can inject arbitrary shell commands. Affected expressions include: `${{ inputs.charts_dir }}`, `${{ inputs.version }}`, `${{ inputs.config }}`, `${{ inputs.install_dir }}`, `${{ inputs.install_only }}`, `${{ inputs.skip_packaging }}`, `${{ inputs.skip_existing }}`, `${{ inputs.skip_upload }}`, `${{ inputs.mark_as_latest }}`, `${{ inputs.packages_with_index }}`, and `${{ inputs.pages_branch }}`. These should be mapped to `env:` variables and referenced as `$VAR_NAME` in the shell script.

Locations:

- `action.yml:68`
- `action.yml:70`
- `action.yml:74`
- `action.yml:78`
- `action.yml:81`
- `action.yml:82`
- `action.yml:85`
- `action.yml:89`
- `action.yml:93`
- `action.yml:97`
- `action.yml:101`
- `action.yml:105`
- `action.yml:109`

### github-env-injection (severity: high)

The `run:` block in action.yml writes the attacker-controlled expression `${{ inputs.install_dir }}` directly to `$GITHUB_PATH` without any sanitization (no `printf '%s' ... | tr -d '\n\r'` step). A malicious value containing newlines could inject additional entries into `$GITHUB_PATH`, enabling path-hijacking attacks. The offending line is: `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`.

Locations:

- `action.yml:82`

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

Fixed all script injection findings in action.yml by: (1) Adding an `env:` block to the step that maps all `${{ inputs.* }}` expressions to environment variables (INPUT_CHARTS_DIR, INPUT_VERSION, INPUT_CONFIG, INPUT_INSTALL_DIR, INPUT_INSTALL_ONLY, INPUT_SKIP_PACKAGING, INPUT_SKIP_EXISTING, INPUT_SKIP_UPLOAD, INPUT_MARK_AS_LATEST, INPUT_PACKAGES_WITH_INDEX, INPUT_PAGES_BRANCH); (2) Replacing all `${{ inputs.* }}` references in the `run:` block with the corresponding `$INPUT_*` environment variable references; (3) Fixing the github-env-injection by sanitizing the install_dir value with `printf '%s' "$INPUT_INSTALL_DIR" | tr -d '\n\r'` before writing to $GITHUB_PATH.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the unsanitized $INPUT_VERSION usage in the GITHUB_PATH write. When INPUT_INSTALL_DIR is empty, the code now sanitizes INPUT_VERSION with `safe_version=$(printf '%s' "$INPUT_VERSION" | tr -d '\n\r')` before using it to construct the install path, and uses `printf '%s\n'` to write to $GITHUB_PATH. This prevents newline injection attacks via the `inputs.version` parameter, matching the sanitization pattern already applied to the INPUT_INSTALL_DIR branch.

