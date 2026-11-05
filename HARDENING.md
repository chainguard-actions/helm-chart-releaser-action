<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.7.0** was hardened automatically. 26 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the composite step 'release' puts GitHub Actions expressions straight into its run: script. Examples: `args+=(--charts-dir "${{ inputs.charts_dir }}")`, `if [[ -n "${{ inputs.version }}" ]]; then`, `install="$RUNNER_TOOL_CACHE/cr/${{ inputs.version }}/$(uname -m)"`, `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`. The same pattern appears for inputs.config, install_only, skip_packaging, skip_existing, skip_upload, mark_as_latest, packages_with_index and pages_branch. An input value containing a quote or $(...) can run any command. Fix: pass each input through env: and expand it in double quotes.

Locations:

- `action.yml:76`
- `action.yml:78`
- `action.yml:87`
- `action.yml:91`

### github-env-injection (severity: high)

The composite step 'release' writes input-derived values to $GITHUB_PATH without the `printf '%s' ... | tr -d '\n\r'` sanitization step. Line 91 is `echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"`, which writes inputs.install_dir directly and unquoted. Line 88 is `echo "$install" >> "$GITHUB_PATH"`, where $install contains ${{ inputs.version }}. A newline in either input adds arbitrary entries to PATH. The step also appends changed_charts.txt to $GITHUB_OUTPUT with `cat` and no sanitization. That file holds chart paths built from inputs.charts_dir.

Locations:

- `action.yml:88`
- `action.yml:91`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 017e5e7..f7753a5 100644
--- a/action.yml
+++ b/action.yml
@@ -63,61 +63,91 @@ outputs:
   chart_version:
     description: "The version of the most recently generated charts; will be set even if no charts have been updated since the last run."
     value: ${{ steps.release.outputs.chart_version }}
-
 runs:
   using: composite
   steps:
     - id: release
+      env:
+        INPUT_VERSION: ${{ inputs.version }}
+        INPUT_CONFIG: ${{ inputs.config }}
+        INPUT_CHARTS_DIR: ${{ inputs.charts_dir }}
+        INPUT_INSTALL_DIR: ${{ inputs.install_dir }}
+        INPUT_INSTALL_ONLY: ${{ inputs.install_only }}
+        INPUT_SKIP_PACKAGING: ${{ inputs.skip_packaging }}
+        INPUT_SKIP_EXISTING: ${{ inputs.skip_existing }}
+        INPUT_SKIP_UPLOAD: ${{ inputs.skip_upload }}
+        INPUT_MARK_AS_LATEST: ${{ inputs.mark_as_latest }}
+        INPUT_PACKAGES_WITH_INDEX: ${{ inputs.packages_with_index }}
+        INPUT_PAGES_BRANCH: ${{ inputs.pages_branch }}
       run: |
         owner=$(cut -d '/' -f 1 <<< "$GITHUB_REPOSITORY")
         repo=$(cut -d '/' -f 2 <<< "$GITHUB_REPOSITORY")
 
         args=(--owner "$owner" --repo "$repo")
-        args+=(--charts-dir "${{ inputs.charts_dir }}")
+        args+=(--charts-dir "$INPUT_CHARTS_DIR")
 
-        if [[ -n "${{ inputs.version }}" ]]; then
-            args+=(--version "${{ inputs.version }}")
+        if [[ -n "$INPUT_VERSION" ]]; then
+            args+=(--version "$INPUT_VERSION")
         fi
 
-        if [[ -n "${{ inputs.config }}" ]]; then
-            args+=(--config "${{ inputs.config }}")
+        if [[ -n "$INPUT_CONFIG" ]]; then
+            args+=(--config "$INPUT_CONFIG")
         fi
 
-        if [[ -z "${{ inputs.install_dir }}" ]]; then
-          install="$RUNNER_TOOL_CACHE/cr/${{ inputs.version }}/$(uname -m)"
-          echo "$install" >> "$GITHUB_PATH"
-          args+=(--install-dir "$install")
+        if [[ -z "$INPUT_INSTALL_DIR" ]]; then
+          install="$RUNNER_TOOL_CACHE/cr/$INPUT_VERSION/$(uname -m)"
+          safe_install=$(printf '%s' "$install" | tr -d '\n\r')
+          echo "$safe_install" >> "$GITHUB_PATH"
+          args+=(--install-dir "$safe_install")
         else
-          echo ${{ inputs.install_dir }} >> "$GITHUB_PATH"
-          args+=(--install-dir "${{ inputs.install_dir }}")
+          safe_install_dir=$(printf '%s' "$INPUT_INSTALL_DIR" | tr -d '\n\r')
+          echo "$safe_install_dir" >> "$GITHUB_PATH"
+          args+=(--install-dir "$safe_install_dir")
         fi
 
-        if [[ -n "${{ inputs.install_only }}" ]]; then
-            args+=(--install-only "${{ inputs.install_only }}")
+        if [[ -n "$INPUT_INSTALL_ONLY" ]]; then
+            args+=(--install-only "$INPUT_INSTALL_ONLY")
         fi
 
-        if [[ -n "${{ inputs.skip_packaging }}" ]]; then
-            args+=(--skip-packaging "${{ inputs.skip_packaging }}")
+        if [[ -n "$INPUT_SKIP_PACKAGING" ]]; then
+            args+=(--skip-packaging "$INPUT_SKIP_PACKAGING")
         fi
 
-        if [[ -n "${{ inputs.skip_existing }}" ]]; then
-            args+=(--skip-existing "${{ inputs.skip_existing }}")
+        if [[ -n "$INPUT_SKIP_EXISTING" ]]; then
+            args+=(--skip-existing "$INPUT_SKIP_EXISTING")
         fi
 
-        if [[ -n "${{ inputs.skip_upload }}" ]]; then
-            args+=(--skip-upload "${{ inputs.skip_upload }}")
+        if [[ -n "$INPUT_SKIP_UPLOAD" ]]; then
+            args+=(--skip-upload "$INPUT_SKIP_UPLOAD")
         fi
 
-        if [[ -n "${{ inputs.mark_as_latest }}" ]]; then
-            args+=(--mark-as-latest "${{ inputs.mark_as_latest }}")
+        if [[ -n "$INPUT_MARK_AS_LATEST" ]]; then
+            args+=(--mark-as-latest "$INPUT_MARK_AS_LATEST")
         fi
 
-        if [[ -n "${{ inputs.packages_with_index }}" ]]; then
-            args+=(--packages-with-index "${{ inputs.packages_with_index }}")
+        if [[ -n "$INPUT_PACKAGES_WITH_INDEX" ]]; then
+            args+=(--packages-with-index "$INPUT_PACKAGES_WITH_INDEX")
         fi
 
-        i
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection, prior-fix

**Notes:**

Fixed the script-injection, file-write and prior-fix findings in action.yml. Nothing was run or tested.

- **Script injection:** all 11 inputs (`version`, `config`, `charts_dir`, `install_dir`, `install_only`, `skip_packaging`, `skip_existing`, `skip_upload`, `mark_as_latest`, `packages_with_index`, `pages_branch`) now reach the `release` step through `env:` as `INPUT_*` variables. The script uses them only as double-quoted `"$INPUT_*"`. The `args` array, its argument boundaries and the `-n` checks are the same as before.
- **`GITHUB_PATH` writes:** both install-dir values (the default cache path and `inputs.install_dir`) have newlines removed with `printf '%s' | tr -d '\n\r'` before they are written. The same cleaned value is what gets passed as `--install-dir`.
- **`GITHUB_OUTPUT` writes:** the contents of `changed_charts.txt` and `chart_version.txt` now have newlines removed before they are appended. `cr.sh` writes each file as a single `key=value` line, so the outputs themselves don't change.

This re-applies what the previous harden did.

