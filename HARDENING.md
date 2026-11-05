<!-- markdownlint-disable -->

# Hardening Report: helm--chart-releaser-action/v1.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **helm--chart-releaser-action/v1.9.0** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the composite step 'release', input-derived values are written to $GITHUB_PATH without first running printf '%s' ... | tr -d '\n\r'. Line 127: `echo "$install_dir" >> "$GITHUB_PATH"` writes inputs.install_dir directly. Line 124: `echo "$install" >> "$GITHUB_PATH"` writes a path that includes inputs.version (`install="$RUNNER_TOOL_CACHE/cr/$version/$(uname -m)"`). $GITHUB_PATH has no heredoc form, so a value containing newlines would add extra attacker-chosen entries to PATH.

Locations:

- `action.yml:124`
- `action.yml:127`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 86199d8..1f15ec1 100644
--- a/action.yml
+++ b/action.yml
@@ -82,6 +82,23 @@ outputs:
 runs:
   using: composite
   steps:
     - id: release
       env:
         charts_dir: ${{ inputs.charts_dir }}
@@ -121,10 +138,12 @@ runs:
 
         if [[ -z "$install_dir" ]]; then
           install="$RUNNER_TOOL_CACHE/cr/$version/$(uname -m)"
-          echo "$install" >> "$GITHUB_PATH"
+          safe_install=$(printf '%s' "$install" | tr -d '\n\r')
+          echo "$safe_install" >> "$GITHUB_PATH"
           args+=(--install-dir "$install")
         else
-          echo "$install_dir" >> "$GITHUB_PATH"
+          safe_install_dir=$(printf '%s' "$install_dir" | tr -d '\n\r')
+          echo "$safe_install_dir" >> "$GITHUB_PATH"
           args+=(--install-dir "$install_dir")
         fi
 

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

In action.yml, the 'release' step now removes newline and carriage-return characters from $install and $install_dir before writing them to $GITHUB_PATH. This puts back the earlier harden's change. The arguments passed to cr.sh are the same as before. The previous patch also added a 17-line hunk at the start of runs: whose content the diff didn't show, so I couldn't restore that part.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

In action.yml, the step no longer appends changed_charts.txt and chart_version.txt to GITHUB_OUTPUT with cat. It now reads each file, removes the 'key=' prefix that cr.sh writes, strips CR/LF with printf | tr -d '\n\r', and writes 'key=$safe'. The output names and values are the same as before for the normal single-line content.

### Iteration 1

**Fixes applied:** publish-gate

**Notes:**

In the "release" step of action.yml, I removed the two lines that wrote changed_charts and chart_version to $GITHUB_OUTPUT with `echo "key=$safe_..."`. In their place the step now does what upstream does: cr.sh writes the files changed_charts.txt and chart_version.txt, and the step runs `cat` on each file into "$GITHUB_OUTPUT" if it exists, then deletes both files. Nothing else in action.yml or cr.sh changed. I haven't re-run the publish checks.

The newline stripping on these two outputs is gone. That should be safe without it, because cr.sh always writes each file as one `key=value` line: the chart list is joined with commas, and the values come from chart directory names and chart versions, not from workflow expressions. The one gap is a chart directory whose name contains a newline, which would split the output across lines.

