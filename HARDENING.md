<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite steps reference actions by a mutable tag instead of a 40-character commit SHA: 'uses: actions/checkout@v4' and 'uses: ruby/setup-ruby@v1'.

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): a ${{ }} expression is written directly into a run: script. Offending line: 'run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"'. The expression only resolves to '--fix' or '', but any ${{ }} inside run: is a finding under this check. Pass the value through an env: variable instead, e.g. AUTOFIX: ${{ inputs.autofix }}, then use if [ "$AUTOFIX" = "true" ] in the script.

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

Pinned actions/checkout@v4 to 11d5960a326750d5838078e36cf38b85af677262 and ruby/setup-ruby@v1 to 26e5b7d197ebf60545ee9667bcbace0be11ccaec. The autofix input now reaches the step through env AUTOFIX. A bash array adds --fix only when AUTOFIX is 'true', so when autofix is off, standardrb gets no empty argument.

