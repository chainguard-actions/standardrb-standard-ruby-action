<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.6.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a `run:` shell command string. The step 'Run Standard Ruby; optionally autofix' uses `${{ inputs.autofix == 'true' && '--fix' || '' }}` directly in the shell command: `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`. The `inputs.autofix` value is caller-controlled and flows through YAML template substitution before the shell sees it, enabling potential command injection. The value should be passed via an `env:` variable and double-quoted in the shell script instead.

Locations:

- `action.yml:53`

### unpinned-uses (severity: high)

The `uses:` reference `ruby/setup-ruby@v1` in action.yml uses a mutable tag (`@v1`) rather than a full 40-character commit SHA. This is explicitly acknowledged in a comment ('unpinned to allow users to get latest rubies') but still represents a supply-chain risk — a compromised or altered tag could execute arbitrary code. It should be pinned to a specific SHA, e.g. `ruby/setup-ruby@<40-char-sha> # v1`.

Locations:

- `action.yml:42`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby; optionally autofix"; move to env: map

Locations:

- `action.yml:54`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned ruby/setup-ruby@v1 to full SHA 95ef2b042f9d7a56d8268cba8559e2842e2ad01b with '# v1' comment. 2. Moved the ${{ inputs.autofix == 'true' && '--fix' || '' }} expression from the run: shell command into an env: block as AUTOFIX_FLAG, then referenced it in the shell script as ${AUTOFIX_FLAG:+"$AUTOFIX_FLAG"} — this safely expands to nothing when empty and to the quoted '--fix' flag when set, preventing script injection while preserving correct behavior.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/release.yml lines 18-19. The original code used unquoted `$tag` (sourced from `github.ref_name`) in `gh release view $tag` and in a bash parameter expansion `${tag/*-*/"$tag" --prerelease}`. Replaced with a safe bash array approach: build `args=("$tag" --generate-notes)`, conditionally append `--prerelease` using `[[ "$tag" == *-* ]]`, then call `gh release view "$tag" || gh release create "${args[@]}"`. All expansions of the workflow-controlled `$tag` value are now properly double-quoted.

