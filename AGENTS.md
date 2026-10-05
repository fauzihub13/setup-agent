# AGENTS.md

## Purpose

This repository is worked on with AI coding agents. This file defines how an
agent should work here: manage context, use persistent project memory, explore
the codebase, modify files, validate changes, and interact with Git.

These instructions are technology-agnostic. First understand the repository's
actual stack, architecture, conventions, and tooling; never assume a framework,
language, or structure.

The core principle:

> **Load the minimum context needed to complete the task correctly. Preserve
> reusable knowledge for future sessions. Never spend context on irrelevant
> information.**

---

## 1. The Three Non-Negotiable Rules

### Rule 1 — Memory First

When `memory/` exists:

```
memory/INDEX.md  →  relevant memory  →  targeted source exploration
```

Do not rediscover what memory already provides.

### Rule 2 — Context Is a Budget

Every file read costs context. Do not read information unlikely to matter. But
never sacrifice correctness to save context: the goal is **minimum sufficient
context**, not minimum context at any cost.

### Rule 3 — Git Is Explicit

```
No explicit commit request → no commit.
No explicit push request    → no push.
```

The agent may modify and validate the working tree. Publication to Git is always
controlled by the user.

---

## 2. Workflow

Preferred progression:

```
User request
  ↓
Persistent memory (read INDEX.md first)
  ↓
Targeted search
  ↓
Relevant source
  ↓
Implementation
  ↓
Targeted validation
  ↓
Memory update (if reusable knowledge was produced)
```

Before starting, ask: *what is the minimum I need to do this safely and
correctly?* Correctness outranks token savings.

---

## 3. Persistent Project Memory

If `memory/` exists it holds concise knowledge for AI-assisted development —
the things a new agent would otherwise spend significant context rediscovering.
It is not a copy of the source code.

**Entry point:** `memory/INDEX.md`. Read it before exploring source. Treat it as
a read-map: identify relevant topics, read only those files, then inspect source.
Never read every file in `memory/` by default; one or two relevant files is
usually enough. If `INDEX.md` is missing but other memory files exist, inspect
selectively and create or improve the index if the repo clearly needs one.

**Memory is not the source of truth.** When memory conflicts with the current
implementation, the repository wins. Verify important assumptions against
source, config, or tests. Fix stale or wrong memory as part of the task.

**Maintain automatically.** After a meaningful task, update memory if it
produced reusable knowledge — without waiting to be asked. Useful knowledge
includes: architectural relationships and dependencies, API behavior,
recurring patterns, framework/library quirks, non-obvious bugs and workarounds,
structural changes, important dev/validation commands, and meaningful
decisions. If a task produced no reusable knowledge, do not touch memory.
Memory is not a session journal.

**Keep it concise and current.** Store facts and decisions, not narrative.
Write plain present-state statements, e.g.:

```markdown
## Authentication
Access tokens are created by the auth service. The API client refreshes
expired tokens before retrying. Do not refresh tokens in individual calls.
```

When behavior changes, rewrite the entry to describe the new state. Do not
append "previously A, now B" unless the history genuinely matters.

**Never store secrets.** No API keys, tokens, passwords, private keys,
credentials, or session secrets. Describe *how* credentials are configured,
never the values.

**Keep `memory/` out of Git.** It is local knowledge. Add it to `.gitignore`
(`/memory/`). Ignore other local agent state (e.g. `ai/`) similarly. Never
commit or push memory as part of normal work.

---

## 4. Exploration

Explore incrementally. Do not begin by reading every file, config, or
directory. Identify the likely location first, then read only relevant code and
its dependencies.

**Search before reading.** Look for a function name, class/type, component,
endpoint, config key, unique string, or file pattern before opening files.
Prefer search tools over broad reads.

**Read small windows.** Do not read large files end to end. Locate the relevant
symbol or section and read the smallest useful portion. Expand only when
surrounding context is genuinely required. This applies to generated files,
large docs, and logs too.

**Skip irrelevant artifacts.** Avoid generated and dependency directories
(`node_modules/`, `vendor/`, `dist/`, `build/`, `coverage/`, `.cache/`, `.tmp/`,
etc.) unless the task explicitly requires them. Determine what is generated from
the repo's own conventions. Do not read lockfiles unnecessarily; prefer a
manifest for dependency info.

**Do not re-read.** If a file or fact was already read this task and is still in
context, do not read it again. Build on what you have. Re-read only if it
changed or a different section is needed.

---

## 5. Implementation

**Follow existing conventions.** Before modifying code, understand enough of the
current implementation to avoid conflicting with established patterns. Look for
existing helpers, services, utilities, or components before creating new ones.
Prefer the project's conventions over personal preference — consistency matters
on shared codebases.

**Respect scope.** Keep changes focused on the request. Do not refactor
unrelated code because you noticed it could improve. Mention unrelated issues in
the final response if useful, but do not fold them into the change. A small task
should produce a small diff.

**Avoid new dependencies and abstractions.** Do not add a dependency,
framework, abstraction, or architectural pattern unless it is genuinely useful.
First check whether the repo already provides the capability. This is not an
absolute ban: if the task truly requires it, add it and explain the trade-off.

**Configuration and secrets.** Inspect existing config conventions before
changing config. Do not assume dev, test, staging, and prod are identical. Keep
environment-specific values external when the architecture expects it. Never
hardcode secrets. If a config change yields reusable knowledge, consider adding
it to memory.

**API and integrations.** Before changing external integration behavior, inspect
the existing request, response, auth, error-handling, and retry conventions.
Do not assume a service follows generic expectations when the repo already
encodes real behavior. Record unusual behavior in memory if it will matter again.

**Architectural changes.** If a task changes the architecture, update memory to
describe the resulting architecture (not the process). Record the reason when a
future engineer would reasonably ask "why was it done this way?".

**Gotchas.** Preserve non-obvious problems that save future investigation:
library behavior differing from docs, unusual endpoint response shapes, required
operation sequences, files that must exist at runtime despite looking unused,
types that cannot be simplified due to external constraints, upstream-limitation
workarounds. Do not fill memory with facts an agent rediscovers instantly.

---

## 6. Validation

Validate using the repository's existing mechanisms. Prefer the smallest check
with meaningful confidence — e.g. a focused test for the changed behavior
before the full suite. Expand when necessary.

Never claim a test, build, or lint succeeded unless it actually ran
successfully. If validation is impossible, say so explicitly in the final
response.

---

## 7. Safety

**Preserve user changes.** Assume existing uncommitted changes may belong to the
user. Do not discard, reset, overwrite, or destroy them to clean the tree.
Inspect repo state and distinguish your changes from pre-existing work before
any operation that could affect unrelated changes.

**Destructive operations need care.** Deleting files, resetting changes,
cleaning untracked files, rewriting history, destructive DB operations, and
modifying production resources. Never do these merely for convenience. If
genuinely necessary, confirm it is consistent with the request and does not
silently destroy unrelated work.

---

## 8. Git Policy

**Commit only when explicitly requested.** Statements like "fix this",
"implement this", "finish this", "apply the changes", or "make it production
ready" are *not* permission to commit. Explicit requests ("commit this", "create
a commit", "commit with this message") are.

**Push only when explicitly requested.** Commit does not imply push. "Commit and
push" authorizes both; "commit the changes" authorizes only commit.

**Before a user-requested commit**, inspect the working tree: review the diff,
confirm which files changed, and ensure local agent state (`memory/`) is not
included. Check for accidentally staged secrets, generated artifacts, or
unrelated user changes. Include only changes relevant to the requested work.

**Branches.** Respect the current branch unless the user asks to switch or the
task clearly requires it. Never assume a branch name is correct across repos;
branch names, hosts, and workflows vary.

Default workflows:

```
Changes requested:  modify → validate → STOP
Explicit commit:    modify → validate → review → commit → STOP
Explicit push:      modify → validate → review → commit → push
```

---

## 9. When to Ask the User

Do not ask merely because more information would be convenient. If the answer
can be found safely in the repo, memory, tests, config, or conventions,
investigate and proceed. Ask when ambiguity could materially change the
implementation, cause destructive behavior, or force a significant decision
that cannot be reasonably inferred. Avoid both unnecessary questions and unsafe
assumptions.

---

## 10. Final Response

Keep it concise. Report the outcome, not the implementation. Include:

1. What was changed.
2. What was validated.
3. Whether memory was updated.
4. Whether Git was left uncommitted.

Do not paste large file sections or narrate the whole investigation unless
asked. Example:

```text
Implemented:
- ...

Validation:
- ...

Memory:
- Updated ... / No update needed.

Git:
- Not committed or pushed (no explicit Git request).
```

---

## 11. Pre-Completion Checklist

- Understood the actual request.
- Checked `memory/INDEX.md` if memory exists; read only relevant memory.
- Explored incrementally; searched before reading large or unknown files.
- Did not re-read context already available.
- Followed existing conventions.
- Kept the change scoped.
- Validated appropriately.
- Preserved unrelated user changes.
- Evaluated memory for new reusable knowledge; fixed stale memory.
- Kept secrets out of source and memory.
- Kept `memory/` out of Git.
- Did not commit unless explicitly requested.
- Did not push unless explicitly requested.