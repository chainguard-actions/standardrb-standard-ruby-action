<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.5.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references two external actions using mutable version tags instead of pinned full-length SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved or hijacked.

Failing references:
- `uses: actions/checkout@v4` (line 22) — should be pinned to a full 40-character commit SHA
- `uses: ruby/setup-ruby@v1` (line 36) — should be pinned to a full 40-character commit SHA

Locations:

- `action.yml:22`
- `action.yml:36`

### script-injection (severity: high)

Sub-rule (a) violation: The 'Run Standard Ruby with autofix' step directly interpolates a GitHub Actions expression inside a `run:` shell command string. The expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is substituted by the YAML template engine before the shell ever sees the command, meaning a caller who controls `inputs.autofix` can inject arbitrary shell content.

Offending line:
  run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"

Fix: move the conditional into an `env:` variable and reference it as a quoted shell variable, e.g.:
  env:
    AUTOFIX: ${{ inputs.autofix }}
  run: |
    fix_flag=""
    if [[ "$AUTOFIX" == 'true' ]]; then fix_flag="--fix"; fi
    bundle exec standardrb $fix_flag --format github --format "Standard::Formatter"

Locations:

- `action.yml:44`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:50`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned actions/checkout@v4 to full SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 (# v4 comment preserved). 2. Pinned ruby/setup-ruby@v1 to full SHA 003a5c4d8d6321bd302e38f6f0ec593f77f06600 (# v1 comment preserved). 3. Fixed script injection in 'Run Standard Ruby with autofix' step: moved ${{ inputs.autofix }} into an env: block as AUTOFIX, then used a bash conditional to set fix_flag before invoking standardrb — no GitHub Actions expressions remain inline in the run: shell string.

