<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **standardrb--standard-ruby-action/v1.5.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the upstream tag is moved:
- `uses: actions/checkout@v4` (line 22)
- `uses: ruby/setup-ruby@v1` (line 37)
These should be pinned to full SHA digests, e.g. `actions/checkout@<40-char-sha> # v4`.

Locations:

- `action.yml:22`
- `action.yml:37`

### script-injection (severity: high)

Sub-rule (a): A `${{ inputs.* }}` expression is interpolated directly inside a `run:` shell command string. In the "Run Standard Ruby with autofix" step (line 46):

  `run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"`

The expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is substituted into the shell command by the Actions runner before the shell parses it. A calling workflow that sets `inputs.autofix` to a value containing shell metacharacters (e.g. `'; malicious_cmd #`) could achieve arbitrary command execution.

Locations:

- `action.yml:46`

### github-env-injection (severity: high)

The "Check for .ruby-version file" step reads the contents of the repository's `.ruby-version` file into the shell variable `RUBY_VERSION` and writes it directly to `$GITHUB_ENV` without sanitization:

  `RUBY_VERSION=$(cat .ruby-version)`
  `echo "ruby-version=$RUBY_VERSION" >> $GITHUB_ENV`

The `.ruby-version` file is part of the checked-out repository and can be controlled by an attacker via a pull request. A malicious `.ruby-version` file containing a newline followed by `EVIL_VAR=injected` would inject an arbitrary environment variable into subsequent steps. The required sanitization (`safe=$(printf '%s' "$RUBY_VERSION" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:31`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.autofix == 'true' && '--fix' || '' }}" appears directly in run: block of step "Run Standard Ruby with autofix"; move to env: map

Locations:

- `action.yml:50`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all four findings in action.yml:
1. Pinned `actions/checkout@v4` to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5` and `ruby/setup-ruby@v1` to SHA `89f90524b88a01fe6e0b732220432cc6142926af`, with original tags preserved as comments.
2. Fixed script-injection/static-inline-injection: moved `${{ inputs.autofix }}` into an `env:` block as `AUTOFIX`, then used a bash array to conditionally add `--fix` only when `$AUTOFIX = 'true'`, keeping argument boundaries clean.
3. Fixed github-env-injection: added `safe=$(printf '%s' "$RUBY_VERSION" | tr -d '\n\r')` before writing to `$GITHUB_ENV` to strip any embedded newlines from the `.ruby-version` file content.

