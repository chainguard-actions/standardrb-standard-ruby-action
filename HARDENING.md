<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Composite action steps reference actions by mutable tags instead of 40-char commit SHAs: 'uses: actions/checkout@v4' (line 20) and 'uses: ruby/setup-ruby@v1' (line 23).

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly in a run: script. Offending line: run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter". Move the input into an env: variable and branch on it in the shell instead.

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

Pinned actions/checkout@v4 and ruby/setup-ruby@v1 to full SHAs (tag kept as comment). Moved inputs.autofix into env INPUT_AUTOFIX and built the optional --fix flag with a bash array so no empty arg is passed when autofix is disabled.

