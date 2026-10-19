# Memory Index

Entry point for persistent project memory used by AI coding agents. Read this
first; then open only the topic files relevant to the current task.

`memory/` is local knowledge, not a copy of the source. Repository is the source
of truth: when memory conflicts with code, follow the code and fix the memory.

## How to use

```
memory/INDEX.md  →  relevant topic file  →  targeted source exploration
```

- Read only the files whose rows match the task. One or two is usually enough.
- Do not read every file by default.
- Keep `memory/` out of Git (`.gitignore` → `/memory/`).

## Topics

Add one row per topic file. Keep entries short and present-state.

| File | Covers |
| --- | --- |
| `architecture.md` | Modules, boundaries, data flow, key dependencies |
| `conventions.md` | Coding patterns, naming, project-specific rules |
| `commands.md` | Build, test, lint, run, and validation commands |
| `integrations.md` | External APIs: auth, request/response, retries, quirks |
| `gotchas.md` | Non-obvious bugs, workarounds, runtime requirements |
| `decisions.md` | Meaningful choices and the reason behind them |

## What belongs here

- Architectural relationships and non-obvious dependencies.
- API behavior and integration conventions.
- Recurring patterns and framework/library quirks.
- Non-obvious bugs, causes, and workarounds.
- Structural changes and meaningful decisions.
- Important development or validation commands.

## What does NOT belong here

- Copies of source code or full file contents.
- A journal of every session or step-by-step narrative.
- Obvious facts an agent rediscovers instantly.
- Secrets: API keys, tokens, passwords, credentials, private keys.

## Entry format

Facts and decisions, present state, scannable:

```markdown
## <Topic>
<Fact or rule in one or two sentences. Include why if non-obvious.>
```

Example:

```markdown
## Authentication
Access tokens are created by the auth service. The API client refreshes
expired tokens before retrying. Do not refresh tokens in individual calls.
```