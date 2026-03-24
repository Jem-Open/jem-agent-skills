---
name: 1password-secret-references
description: >
  Enforces safe secret handling using 1Password CLI and secret references (op:// URIs)
  during local development. Use this skill whenever a coding session involves secrets,
  API keys, tokens, credentials, database connection strings, environment variables with
  sensitive values, .env files, or any authentication/authorization configuration.
  Also trigger when the user asks you to hit an API endpoint, test a webhook, run a curl
  command with auth, or access any external service that requires credentials. If you are
  about to run any command that involves a secret value — stop and consult this skill first.
  The core rule: never resolve, print, log, store, or expose a secret value in the terminal,
  shell history, files, or your own context. Use op:// references and op run exclusively.
---

# 1Password Secret References

## Why this skill exists

Coding agents default to the path of least resistance with secrets — resolving them into
shell variables, printing them to stdout, or writing them to files. This leaks secrets into
terminal scrollback, shell history, the agent's context window, and process tables. This
skill enforces a single principle: **secrets are resolved only inside the `op run` subprocess
boundary, never in the agent's visible shell.**

## Prerequisites

- 1Password desktop app installed and running
- 1Password CLI (`op`) installed and available on PATH
- Biometric unlock enabled (the desktop app authenticates the CLI)

Before doing anything with secrets, verify the CLI is authenticated:

```bash
op whoami
```

If this fails, tell the user: "Please unlock your 1Password desktop app so the CLI can
authenticate via biometric." Do not attempt to handle authentication yourself — no session
tokens, no `op signin`, no credentials.

## The one rule: `op run` for everything

Every command that needs a secret gets wrapped in `op run`. This resolves `op://` references
inside an isolated subprocess. The parent shell — and therefore you, the agent — never sees
the plaintext value.

### Direct execution

```bash
op run --env-file=.env -- python manage.py runserver
```

### Docker Compose (env var passthrough)

In `docker-compose.yml`, declare environment variables without values so they inherit from
the parent process:

```yaml
services:
  api:
    environment:
      - DATABASE_URL
      - STRIPE_SECRET_KEY
```

Then run:

```bash
op run --env-file=.env -- docker-compose up
```

The secrets resolve in the `op run` subprocess and pass through to the container. They never
appear in the agent's terminal or context.

### Inline env vars for one-off commands

For quick operations like API calls, declare the `op://` reference inline:

```bash
op run --env FRESHDESK_KEY=op://Vault/freshdesk/api-key -- \
  curl -s -H "Authorization: Bearer $FRESHDESK_KEY" \
  https://company.freshdesk.com/api/v2/tickets
```

The response body (data) is fine to read. The secret (the key) stays inside the subprocess.

This pattern applies to any HTTP tool — `curl`, `httpie`, `wget`, or a Python/Node script
that reads from `os.environ` / `process.env`.

### Running test scripts that need secrets

```bash
op run --env-file=.env -- pytest
op run --env-file=.env -- npm test
op run --env-file=.env -- node scripts/seed.js
```

## .env file convention

The `.env` file contains only `op://` references, never real values:

```
DATABASE_URL=op://Engineering/postgres-prod/connection-string
STRIPE_SECRET_KEY=op://Engineering/stripe/secret-key
FRESHDESK_API_KEY=op://Engineering/freshdesk/api-key
```

This file is safe to create, read, and edit — it contains no secrets. Still add `.env` to
`.gitignore` as a safety net against someone manually replacing a reference with a real value.

## Secret discovery

The user provides `op://` references. If you don't know the reference path for a secret,
ask the user. Do not guess or assume vault/item paths.

### Vault metadata browsing (allowed)

You may run these commands to help the user locate the right reference:

```bash
op vault list
op item list --vault=VaultName
```

These return metadata (names, IDs) — not secret values. Use them only when the user asks
for help finding a reference. Never pipe their output into other commands or use them to
construct `op read` calls.

## Writing application code

Reading environment variables in code is always safe — no secret is present at code-writing
time:

```python
import os
db_url = os.environ['DATABASE_URL']
```

```javascript
const dbUrl = process.env.DATABASE_URL;
```

```go
dbUrl := os.Getenv("DATABASE_URL")
```

The secret only exists when the code runs inside an `op run` subprocess.

## Hidden characters in secrets

1Password CLI can append trailing newlines or whitespace to secret values. This silently
breaks API keys, tokens, and connection strings. Always apply defensive trimming in
application code:

```python
api_key = os.environ['API_KEY'].strip()
```

```javascript
const apiKey = process.env.API_KEY?.trim();
```

```go
apiKey := strings.TrimSpace(os.Getenv("API_KEY"))
```

If a request using a valid-looking secret fails with an authentication error, the first
diagnostic step is hidden trailing characters — not re-reading or re-resolving the secret.

## Banned operations — hard stops

Never run any of these. If you find yourself constructing one of these commands, stop
immediately and restructure using `op run`.

| Banned pattern | Why it leaks |
|---|---|
| `op read op://...` | Returns plaintext secret to stdout — visible to agent and shell history |
| `op item get --field password` | Same as above |
| `export SECRET=$(op read ...)` | Secret lands in shell environment and history |
| `SECRET=$(op read ...)` | Secret in shell variable, visible in agent context |
| `echo $SECRET_VAR` | Prints secret to terminal |
| `printf`, `cat`, `env`, `printenv` on secrets | Surfaces secret values |
| Writing resolved values to any file | Persists secret on disk |
| Piping `op read` or `op item get` to any command | Secret transits through stdout |

### Why `$(op read ...)` is specifically dangerous

When the shell encounters `$(op read op://Vault/item/field)`:

1. `op read` executes and returns the plaintext secret to stdout
2. The shell substitutes it into the parent command — the full command string now contains the real secret
3. The agent sees the expanded command in the tool result — the secret is now in the conversation context
4. Shell history logs the resolved command
5. The process table shows the secret in command arguments while running

With `op run`, none of this happens. Resolution occurs inside the subprocess. The parent
shell and the agent only ever see variable names.

## Pre-commit hook setup (gitleaks)

Set up a pre-commit hook to catch accidental secret commits, regardless of whether the agent
or a human made the mistake.

### Using the pre-commit framework

Add to `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.21.2
    hooks:
      - id: gitleaks
```

Then install:

```bash
pre-commit install
```

### Using a raw git hook

If the project doesn't use the pre-commit framework:

```bash
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh
gitleaks protect --staged --no-banner
EOF
chmod +x .git/hooks/pre-commit
```

This requires `gitleaks` to be installed (`brew install gitleaks` or equivalent).

### What gitleaks catches

It scans staged files for patterns that look like real secrets — API keys, tokens, passwords,
connection strings. It will not flag `op://` references since those are not secrets. This is
the safety net beneath the skill's behavioural rules.

## Recovery protocol

If a secret is accidentally surfaced in the terminal or in the agent's context:

1. **Flag it immediately** — tell the user a secret was exposed
2. **Advise rotation** — the user should rotate the compromised secret in 1Password
3. **Clear terminal scrollback:**
   ```bash
   clear && printf '\033[3J'
   ```
4. **Restart the agent session** — the secret is in the conversation context and cannot be removed; starting a new session is the only way to purge it
5. **Check shell history** — remove any commands that contain the secret:
   ```bash
   # Find and remove the offending line from history
   history
   # Then edit ~/.bash_history or ~/.zsh_history manually
   ```

## Quick reference

| I need to... | Do this |
|---|---|
| Run a command that needs secrets | `op run --env-file=.env -- command` |
| Hit an API endpoint with auth | `op run --env VARNAME=op://v/i/f -- curl ...` |
| Start Docker services with secrets | `op run --env-file=.env -- docker-compose up` |
| Run tests that need secrets | `op run --env-file=.env -- pytest` |
| Find a secret reference path | Ask the user, or `op item list --vault=Name` |
| Create/edit a .env file | Use only `op://` references, never real values |
| Read a secret in application code | `os.environ['KEY'].strip()` — trim always |
| Check if CLI is authenticated | `op whoami` |
