<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both `uses:` references in action.yml are pinned to mutable tag refs rather than immutable 40-character commit SHAs. If the upstream action tags are moved (e.g. by a supply-chain compromise), the action will silently execute different code. Failing references: `actions/checkout@v4` (line 20) and `ruby/setup-ruby@v1` (line 22). These should be pinned to their full SHA digests, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:20`
- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is directly interpolated inside a `run:` shell command string on line 31. The offending line is: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`. The value of `inputs.autofix` is caller-controlled and is substituted into the shell command by the Actions runner before the shell ever sees it. An attacker who controls the calling workflow can supply a value containing shell metacharacters (`;`, `|`, `$(...)`, etc.) to achieve arbitrary command execution. The fix is to move the input into an `env:` variable and reference it as a quoted shell variable, e.g.: `env: AUTOFIX: ${{ inputs.autofix }}` then `run: if [[ "$AUTOFIX" == 'true' ]]; then bundle exec standardrb --fix ...; else bundle exec standardrb ...; fi`.

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

1. Pinned actions/checkout@v4 to commit SHA 11d5960a326750d5838078e36cf38b85af677262 (# v4 comment preserved). 2. Pinned ruby/setup-ruby@v1 to commit SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8 (# v1 comment preserved). 3. Fixed script injection on line 31/32: moved inputs.autofix into an env var AUTOFIX and replaced the inline ${{ }} ternary expression with a bash if/else conditional that references $AUTOFIX safely.

