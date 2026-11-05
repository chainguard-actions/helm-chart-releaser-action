<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.8.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The composite step 'release' writes input-derived values to $GITHUB_PATH without first stripping newlines (printf '%s' ... | tr -d '\n\r'). Line 116: `echo "$install" >> "$GITHUB_PATH"`, where install="$RUNNER_TOOL_CACHE/cr/$version/$(uname -m)" and $version comes from inputs.version. Line 119: `echo "$install_dir" >> "$GITHUB_PATH"`, where $install_dir comes from inputs.install_dir. If either value contains a newline, extra PATH entries get added for every later step. $GITHUB_PATH has no heredoc form, so the newlines must be removed before the write. (The step also does `cat changed_charts.txt >> "$GITHUB_OUTPUT"` (lines 168-172) with content built by cr.sh from git paths and tags. Chart names are split on newlines by readarray and git tags cannot contain newlines, so this part is lower risk.)

Locations:

- `action.yml:116`
- `action.yml:119`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index acbedf9..c5a6442 100644
--- a/action.yml
+++ b/action.yml
@@ -79,6 +79,23 @@ outputs:
 runs:
   using: composite
   steps:
     - id: release
       env:
         charts_dir: ${{ inputs.charts_dir }}
@@ -113,10 +130,12 @@ runs:
 
         if [[ -z "$install_dir" ]]; then
           install="$RUNNER_TOOL_CACHE/cr/$version/$(uname -m)"
-          echo "$install" >> "$GITHUB_PATH"
+          safe_path=$(printf '%s' "$install" | tr -d '\n\r')
+          printf '%s\n' "$safe_path" >> "$GITHUB_PATH"
           args+=(--install-dir "$install")
         else
-          echo "$install_dir" >> "$GITHUB_PATH"
+          safe_path=$(printf '%s' "$install_dir" | tr -d '\n\r')
+          printf '%s\n' "$safe_path" >> "$GITHUB_PATH"
           args+=(--install-dir "$install_dir")
         fi
 

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

action.yml: both writes to $GITHUB_PATH (the default tool-cache install path and the install_dir input) now strip \n and \r with printf '%s' | tr -d '\n\r' before writing. This is the same change the previous harden made (prior-fix). The arguments passed to cr.sh are unchanged. The previous patch also added 17 lines near the top of runs:, but the diff did not show what they were, so they were not re-added. The changed_charts.txt and chart_version.txt writes to $GITHUB_OUTPUT were left as they were: the analysis rated them lower risk and did not flag them.

