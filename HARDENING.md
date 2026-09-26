<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: A GitHub Actions expression is directly interpolated inside a `run:` shell command string. The step 'Run Standard Ruby with autofix' contains `${{ inputs.autofix == 'true' && '--fix' || '' }}` embedded directly in the shell command: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github ...`. Any `${{ ... }}` expression inside a `run:` block is evaluated by the Actions template engine before the shell ever sees it, allowing an attacker who controls `inputs.autofix` to inject arbitrary shell content. The value should be routed through an `env:` variable and double-quoted in the shell instead.

Locations:

- `action.yml:31`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten:
- `uses: actions/checkout@v4` (line 20) — should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`
- `uses: ruby/setup-ruby@v1` (line 22) — should be pinned to a full SHA

Locations:

- `action.yml:20`
- `action.yml:22`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4; (2) Pinned ruby/setup-ruby@v1 to SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8 # v1; (3) Moved the ${{ inputs.autofix == 'true' && '--fix' || '' }} expression out of the run: block into an env: variable AUTOFIX_FLAG, and referenced it in the shell as ${AUTOFIX_FLAG:+"$AUTOFIX_FLAG"} to safely handle the empty-string case (no extra empty argument when autofix is false).

