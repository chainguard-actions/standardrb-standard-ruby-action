<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character SHA digests. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: `actions/checkout@v4` and `ruby/setup-ruby@v1`. Both should be pinned to their full commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `action.yml:19`
- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. The step 'Run Standard Ruby with autofix' contains: `bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`. The value of `inputs.autofix` is controlled by the caller and is substituted into the shell command by the YAML template engine before the shell processes it. An attacker could supply a value containing shell metacharacters (e.g. `'; malicious-command #`) to achieve arbitrary command execution. The fix is to move the input into an `env:` variable and reference it as a quoted shell variable: `env: AUTOFIX: ${{ inputs.autofix }}` then use `"$AUTOFIX"` inside the run block.

Locations:

- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned actions/checkout@v4 to commit SHA 11d5960a326750d5838078e36cf38b85af677262 and ruby/setup-ruby@v1 to commit SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8, preserving the original tag in a comment. 2. Fixed script injection (both findings): removed the ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: block by moving inputs.autofix into an env: variable (AUTOFIX: ${{ inputs.autofix }}) and using a plain bash if/else to conditionally add --fix, eliminating any possibility of shell metacharacter injection.

