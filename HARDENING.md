<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: `actions/checkout@v4` (line 20) and `ruby/setup-ruby@v1` (line 22). These should be replaced with their full SHA digests, e.g. `actions/checkout@<40-char-sha> # v4`.

Locations:

- `action.yml:20`
- `action.yml:22`

### script-injection (severity: high)

Rule (a) violation: A `${{ }}` expression is interpolated directly inside a `run:` shell command string. On line 31, `${{ inputs.autofix == 'true' && '--fix' || '' }}` is substituted into the shell command before the shell processes it. Although the expression is a ternary that yields `--fix` or an empty string, the `inputs.autofix` value is caller-controlled and the expression is expanded inline in the shell script, making it a script-injection vector. The fix is to move the input into an `env:` variable and reference it safely in the shell logic. Offending line: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`

Locations:

- `action.yml:31`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4. (2) Pinned ruby/setup-ruby@v1 to SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8 # v1. (3) Eliminated the inline ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: block by moving inputs.autofix into an env: variable (AUTOFIX) and using a shell if/else to conditionally pass --fix to standardrb.

