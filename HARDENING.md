<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a `run:` shell command string. The expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is substituted by the YAML template engine before the shell executes the command, allowing an attacker who controls the `inputs.autofix` value to inject arbitrary shell commands. Offending line: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`

Locations:

- `action.yml:29`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable tag refs instead of pinned 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten. Failing references: `actions/checkout@v4` (line 19) and `ruby/setup-ruby@v1` (line 22).

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

Fixed all three findings in action.yml:
1. Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 # v4
2. Pinned ruby/setup-ruby@v1 to SHA 651ff0d62c5c79bed6d53ec518387aae20e72c1a # v1
3. Removed the inline ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: block. Instead, the raw input is captured in an env: var (AUTOFIX: ${{ inputs.autofix }}) and the shell script uses a plain if/else to conditionally pass --fix to standardrb, eliminating the injection risk.

