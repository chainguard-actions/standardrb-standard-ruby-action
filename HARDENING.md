<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised:
- `uses: actions/checkout@v4` (line 20) — should be pinned to a full SHA
- `uses: ruby/setup-ruby@v1` (line 23) — should be pinned to a full SHA

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Rule (a) violation: A GitHub Actions expression is directly interpolated inside a `run:` shell command string. On line 31, `${{ inputs.autofix == 'true' && '--fix' || '' }}` is expanded by the Actions template engine before the shell ever sees the command. An attacker who controls the `inputs.autofix` value (e.g. via `workflow_dispatch` or a calling workflow) can inject arbitrary shell metacharacters. The offending line is:
  `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`
Fix: move the expression into an `env:` variable and quote the shell expansion, e.g.:
  `env:\n  AUTOFIX: ${{ inputs.autofix }}\nrun: |\n  flag=""\n  if [[ "$AUTOFIX" == "true" ]]; then flag="--fix"; fi\n  bundle exec standardrb $flag --format github --format "Standard::Formatter"`

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

Fixed all three findings in hardened/action/action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262; (2) Pinned ruby/setup-ruby@v1 to SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8; (3) Eliminated script injection by moving ${{ inputs.autofix }} into an env: block as AUTOFIX and using a bash conditional to set the --fix flag, so no GitHub Actions expression is ever interpolated directly into the shell command string.

