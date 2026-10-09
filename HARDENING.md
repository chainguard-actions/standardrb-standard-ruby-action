<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: A GitHub Actions expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is directly interpolated inside a `run:` shell command string. The value of `inputs.autofix` is caller-controlled and is expanded by the template engine before the shell processes it, enabling script injection. The offending line is: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`. Fix: move the conditional logic into the shell script using an `env:` variable and quote it properly.

Locations:

- `action.yml:30`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable tag refs instead of pinned 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten:
- `uses: actions/checkout@v4` (line 20) — should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`
- `uses: ruby/setup-ruby@v1` (line 23) — should be pinned to a full SHA

Locations:

- `action.yml:20`
- `action.yml:23`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262; (2) Pinned ruby/setup-ruby@v1 to SHA e81a8fa391b11a595c1b7ed84177cc0fe02faf83; (3) Removed the ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: block by moving inputs.autofix into an env: variable (AUTOFIX) and implementing the conditional flag logic in the shell script itself.

