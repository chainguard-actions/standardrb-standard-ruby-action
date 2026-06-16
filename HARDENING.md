<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is interpolated directly inside a `run:` shell command string. On line 30, `${{ inputs.autofix == 'true' && '--fix' || '' }}` is embedded directly in the shell command `bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github ...`. An attacker controlling the `autofix` input could inject arbitrary shell commands via this expression before the shell ever sees it. The fix is to move the value into an `env:` variable and reference it with a quoted shell expansion.

Locations:

- `action.yml:30`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable tag refs instead of full 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved or hijacked: (1) `actions/checkout@v4` (line 19); (2) `ruby/setup-ruby@v1` (line 22). Both should be pinned to their full SHA, e.g. `actions/checkout@<40-char-sha> # v4`.

Locations:

- `action.yml:19`
- `action.yml:22`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5; (2) Pinned ruby/setup-ruby@v1 to SHA 89f90524b88a01fe6e0b732220432cc6142926af; (3) Moved the ${{ inputs.autofix == 'true' && '--fix' || '' }} expression out of the run: shell string into an env: block as AUTOFIX_FLAG, and referenced it in the shell command using ${AUTOFIX_FLAG:+"$AUTOFIX_FLAG"} to safely handle the empty-string case without passing an empty positional argument.

