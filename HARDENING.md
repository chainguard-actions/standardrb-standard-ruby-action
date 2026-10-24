<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite action steps point to mutable tags instead of full 40-character commit SHAs: 'uses: actions/checkout@v4' (line 20) and 'uses: ruby/setup-ruby@v1' (line 23).

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly inside a run: script. Offending line: run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter". Fix: compute the flag in the shell from an env var (e.g. env: AUTOFIX: ${{ inputs.autofix }}, then if [ "$AUTOFIX" = "true" ]; then ...).

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

Pinned actions/checkout to 11d5960a326750d5838078e36cf38b85af677262 (v4) and ruby/setup-ruby to 7f797815d8adceb1c412e7abefdd1ab4abc47a5f (v1). Moved inputs.autofix into env AUTOFIX. The --fix flag is now added in bash through an array only when AUTOFIX is 'true', so it is left out completely otherwise, as upstream did.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

Pinned actions/checkout@v4 to 11d5960a326750d5838078e36cf38b85af677262 and ruby/setup-ruby@v1 to 7f797815d8adceb1c412e7abefdd1ab4abc47a5f in action.yml, keeping tag comments.

### Iteration 3

**Fixes applied:** unpinned-uses

**Notes:**

Pinned ruby/setup-ruby@v1 to 7f797815d8adceb1c412e7abefdd1ab4abc47a5f (# v1) in action.yml. actions/checkout was already pinned in a previous iteration.

