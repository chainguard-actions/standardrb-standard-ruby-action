<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tag is moved or hijacked:
- `actions/checkout@v4` (mutable tag `v4`)
- `ruby/setup-ruby@v1` (mutable tag `v1`)
These should be pinned to their full SHA digests, e.g. `actions/checkout@<40-hex-sha> # v4`.

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` block for the 'Run Standard Ruby with autofix' step directly interpolates a GitHub Actions expression inside the shell command string:

  run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"

The expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is expanded by the Actions template engine before the shell ever sees the command. An attacker who controls the `autofix` input (e.g. via `workflow_dispatch` or a calling workflow) could inject shell metacharacters. The fix is to move the input into an `env:` variable and reference it as a quoted shell variable, e.g.:

  env:
    AUTOFIX: ${{ inputs.autofix }}
  run: |
    if [ "$AUTOFIX" = 'true' ]; then EXTRA_ARGS='--fix'; else EXTRA_ARGS=''; fi
    bundle exec standardrb $EXTRA_ARGS --format github --format "Standard::Formatter"

Locations:

- `action.yml:30`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in hardened/action/action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4; (2) Pinned ruby/setup-ruby@v1 to SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8 # v1; (3) Eliminated script injection by moving inputs.autofix into an env: variable (AUTOFIX) and replacing the inline ${{ }} expression with a safe shell conditional that sets EXTRA_ARGS, using ${EXTRA_ARGS:+"$EXTRA_ARGS"} to avoid passing an empty argument when autofix is not 'true'.

