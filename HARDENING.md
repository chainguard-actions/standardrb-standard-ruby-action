<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: A `${{ }}` expression is directly interpolated inside a `run:` shell command string. The step 'Run Standard Ruby with autofix' uses `${{ inputs.autofix == 'true' && '--fix' || '' }}` directly in the shell command: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`. YAML template substitution occurs before the shell ever sees the string, so an attacker-controlled `inputs.autofix` value could inject arbitrary shell commands. The fix is to move the input into an `env:` variable and reference it as a quoted shell variable, e.g. `env: AUTOFIX: ${{ inputs.autofix }}` and then use `"$AUTOFIX"` in the run script.

Locations:

- `action.yml:30`

### unpinned-uses (severity: high)

Two `uses:` references are pinned to mutable tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten:
- `uses: actions/checkout@v4` (mutable tag `v4`)
- `uses: ruby/setup-ruby@v1` (mutable tag `v1`)
These should be pinned to full SHA digests, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:18`
- `action.yml:21`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 and ruby/setup-ruby@v1 to SHA 824c0e29fdfc7d689c191c27acc969d2d04b7dd8. (2) Removed the inline ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: block; moved inputs.autofix into an env: variable (AUTOFIX) and used shell if-logic to conditionally set fix_flag, passing it with ${fix_flag:+"$fix_flag"} to avoid an empty argument when autofix is not 'true'.

