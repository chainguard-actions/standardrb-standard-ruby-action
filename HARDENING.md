<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.5.0** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite steps reference actions by a mutable tag instead of a full 40-character commit SHA: 'uses: actions/checkout@v4' (line 23) and 'uses: ruby/setup-ruby@v1' (line 39).

Locations:

- `action.yml:23`
- `action.yml:39`

### script-injection (severity: high)

Sub-rule (a): an expression is placed directly inside a run: script. Offending line: run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter". Fix: compute the flag in an env: variable and expand it in the shell instead.

Locations:

- `action.yml:49`

### github-env-injection (severity: high)

The step 'Check for .ruby-version file' reads the .ruby-version file into RUBY_VERSION and writes it with echo "ruby-version=$RUBY_VERSION" >> $GITHUB_ENV, with no printf '%s' ... | tr -d '\n\r' step first. The file lives in the checked-out repository, and the directory it is read from comes from inputs.workdir, so both can be controlled through a pull request. A multi-line file could therefore inject extra environment variables (e.g. LD_PRELOAD, BASH_ENV, NODE_OPTIONS) into later steps.

Locations:

- `action.yml:33`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:50`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 4950a40..1a73368 100644
--- a/action.yml
+++ b/action.yml
@@ -1,7 +1,6 @@
 name: Standard Ruby
 description: Lint and auto-fix your Ruby code with Standard Ruby.
 author: Justin Searls <searls@gmail.com>
-
 inputs:
   ruby-version:
     description: The Ruby version specifier to pass to ruby/setup-ruby
@@ -14,14 +13,28 @@ inputs:
   workdir:
     description: The working directory from which to run this action's commands. Default '.'
     default: '.'
-
 runs:
   using: composite
-
   steps:
     - name: Checkout code
-      uses: actions/checkout@v4
-
+      uses: actions/checkout@34e114876b0b11c390a56381ad16ebd13914f8d5 # v4
     - name: Check for .ruby-version file
       id: use_ruby_version_or_default
       shell: bash
@@ -34,21 +47,23 @@ runs:
         else
           echo "No .ruby-version file found, using default"
         fi
-
     - name: Set up Ruby
-      uses: ruby/setup-ruby@v1
+      uses: ruby/setup-ruby@003a5c4d8d6321bd302e38f6f0ec593f77f06600 # v1
       with:
         ruby-version: ${{ env.ruby-version || inputs.ruby-version }}
         bundler-cache: true
         working-directory: ${{ inputs.workdir }}
-
     - name: Run Standard Ruby with autofix
       id: standardrb
       shell: bash
       working-directory: ${{ inputs.workdir }}
-      run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"
+      env:
+        AUTOFIX: ${{ inputs.autofix }}
+      run: |
+        fix_flag=""
+        if [[ "$AUTOFIX" == 'true' ]]; then fix_flag="--fix"; fi
+        bundle exec standardrb $fix_flag --format github --format "Standard::Formatter"
       continue-on-error: true
-
     - name: Check for modified files
       id: check_changes
       shell: bash
@@ -59,7 +74,6 @@ runs:
         else
           echo "standardrb_autofixes_found=false" >> $GITHUB_ENV
         fi
-
     - name: Commit autofixes
       shell: bash
       if: env.standardrb_autofixes_found == 'true'
@@ -72,14 +86,12 @@ runs:
         git commit -m "Apply Standard Ruby autofixes" || echo "No changes to commit"
         git push
         echo "::endgroup::"
-
     - name: Fail the build if Standard Ruby failed
       shell: bash
       if: ${{ steps.standardrb.outcome == 'failure' }}
       run: |
         echo "::error::Standard Ruby found issues in your code."
         exit 1
-
 branding:
   icon: code
   color: yellow

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection, prior-fix

**Notes:**

Pinned actions/checkout to 11d5960a326750d5838078e36cf38b85af677262 (# v4) and ruby/setup-ruby to 7f797815d8adceb1c412e7abefdd1ab4abc47a5f (# v1). Newlines and carriage returns are now stripped from the .ruby-version value before it is written to $GITHUB_ENV, and $GITHUB_ENV is quoted. Moved the autofix expression into env as AUTOFIX. The --fix flag is now built in a bash array and only added when AUTOFIX is 'true', so it adds no empty argument when autofix is off. The previous harden's security changes (pinning and moving the expression into env) are applied again.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned actions/checkout@v4 to 11d5960a326750d5838078e36cf38b85af677262 (# v4) in action.yml.

