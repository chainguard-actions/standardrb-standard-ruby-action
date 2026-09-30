<!-- markdownlint-disable -->

# Hardening Report: standardrb--standard-ruby-action/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **standardrb--standard-ruby-action/v1.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite action steps reference dependencies by mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks: if the upstream repository is compromised or the tag is moved, malicious code could be silently injected.

Failing references:
- `uses: actions/checkout@v4` (line 19) — should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`
- `uses: ruby/setup-ruby@v1` (line 22) — should be pinned to a full SHA, e.g. `ruby/setup-ruby@32110d4e311bd8996b2a82bf2a43b714ccc91777 # v1`

Locations:

- `action.yml:19`
- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): A `${{ }}` expression is interpolated directly inside a `run:` shell command string. On line 31, the expression `${{ inputs.autofix == 'true' && '--fix' || '' }}` is substituted into the shell command before the shell parses it. A calling workflow can supply any string as `inputs.autofix`; if the expression evaluates to an attacker-controlled value, shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) in the result will be interpreted by the shell, enabling arbitrary command injection.

Offending line:
```
run: bundle exec standardrb ${{ inputs.autofix == 'true' && '--fix' || '' }} --format github --format "Standard::Formatter"
```

Remediation: Move the flag selection into the shell script itself using an `env:` variable and a quoted shell conditional, e.g.:
```yaml
env:
  AUTOFIX: ${{ inputs.autofix }}
run: |
  flag=""
  if [ "$AUTOFIX" = "true" ]; then flag="--fix"; fi
  bundle exec standardrb ${flag:+"$flag"} --format github --format "Standard::Formatter"
```

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

Three fixes applied to hardened/action/action.yml: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262; (2) Pinned ruby/setup-ruby@v1 to SHA 14594264cd68ce8a2345dd349bc3d138a4ef85c8; (3) Fixed script injection on line 31 by moving ${{ inputs.autofix }} into an env: block as AUTOFIX, then using a shell conditional to set the --fix flag safely with ${flag:+"$flag"} expansion.

