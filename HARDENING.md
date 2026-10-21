<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite steps pin actions to a version tag instead of a full commit SHA: 'uses: actions/checkout@v4' (line 20) and 'uses: ruby/setup-ruby@v1' (line 23).

Locations:

- `action.yml:20`
- `action.yml:23`

### script-injection (severity: high)

Rule (a): a ${{ }} expression is placed directly inside a run: script: "run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format \"Standard::Formatter\"". The rule bans any ${{ }} expression inside run:. The fix is to pass inputs.autofix in through an env: variable and check it with quotes in the shell.

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

Pinned actions/checkout@v4 to 11d5960a326750d5838078e36cf38b85af677262 and ruby/setup-ruby@v1 to b1da4809b005e0178fe08a1d57317bffc00b7b4c, with the tag kept as a trailing comment on each. Moved inputs.autofix into the env var INPUT_AUTOFIX. The run script now checks it with [ "$INPUT_AUTOFIX" = "true" ] and adds --fix through a bash array. When autofix is off, the empty array adds no argument, so the command gets the same arguments as before. standardrb is still the last command in the step, so the step's exit status and continue-on-error work as before. Not tested by running the action.

### Iteration 2

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved inputs.autofix into the AUTOFIX env var. The shell now picks the --fix flag itself and passes it with ${FIX:+"$FIX"}, so no empty argument is added when autofix is off. Behavior is unchanged from upstream.

