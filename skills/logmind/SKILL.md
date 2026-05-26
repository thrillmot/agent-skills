---
name: logmind
description: |
  MUST be loaded for any task in a project that uses logmind (detect by:
  .logmind/config.yml at repo root, or AGENTS.md / CLAUDE.md mentioning
  logmind, or docs/decisions.md present). Use BEFORE writing >20 lines of
  new code, BEFORE choosing between alternatives, BEFORE adding a
  dependency, BEFORE modifying existing functionality, BEFORE making any
  security or performance trade-off, BEFORE renaming or moving any
  significant module. Logging is part of the work, not after it. Also use
  to read prior decisions before starting any task in such a project so
  you don't re-litigate something already decided.
---

# logmind: log decisions as you work

If the current project contains `.logmind/config.yml` or its `AGENTS.md`
mentions logmind, this skill applies. Log a decision **before** writing
non-trivial code, not after.

## When to log

Log a decision whenever you:

- Make an architectural or design choice
- Choose between alternative approaches
- Write significant new code (> 20 lines or a new module)
- Modify existing functionality in a non-obvious way
- Add or remove a dependency
- Make a security or performance decision

When in doubt, log it. A short decision entry is cheap; recovering missing
context months later is not.

## How to log

### CLI (preferred)

```bash
logmind log "Use PostgreSQL for primary database" \
  -r "Need ACID compliance and complex joins" \
  -a "MongoDB" -a "SQLite" \
  -i "Connection pooling required" \
  -i "Schema migrations needed"
```

### Python

```python
from logmind import log

log("Use PostgreSQL for primary database",
    reasoning="Need ACID compliance and complex joins",
    alternatives=["MongoDB", "SQLite"],
    implications=["Connection pooling required", "Schema migrations needed"])
```

## What `logmind log` does automatically

A single `logmind log` invocation handles all of this — you do NOT need to
follow up with manual `git add`, `git commit`, or `logmind timeline --write`:

1. Appends the decision entry to the active log file (default branch →
   `docs/decisions.md`; feature branch → `docs/decisions-branches/<branch>.md`).
2. Archives the oldest decision if `decisions.md` exceeds `max_recent` (rotation).
3. Regenerates `docs/file-structure.md` (default branch only — on feature
   branches the tree snapshot diverges and would conflict).
4. **Regenerates `docs/timeline.md` on every branch (v0.2.3+)** — the derived
   index that `check-derived-docs` CI verifies. Auto-staged into the same
   commit, so no extra `logmind timeline --write` step is needed.
5. Stages the touched files (see `--stage` below).
6. Creates the commit with message `logmind: <decision>`.
7. Pushes to remote (`auto_push: true` in config).

## `--stage scoped` (default) vs `--stage all`

- **`--stage scoped` (default — use this 95% of the time):** stages ONLY
  the decision file, `file-structure.md`, archive (if rotated), and
  `timeline.md` (if regenerated). Unrelated working-tree changes stay
  unstaged — log the decision first, then commit the code separately.
- **`--stage all`:** stages everything in the working tree alongside the
  decision. Use ONLY when the code change and the decision genuinely belong
  in the same atomic commit (rare — usually you want them as two commits in
  the same PR so the decision is reviewable on its own).

Both modes regenerate and stage `timeline.md`. If you ever find yourself
running `logmind timeline --write docs/timeline.md` after a `logmind log`,
something's wrong (most likely an older logmind version — check `logmind
--version` and `logmind doctor`).

## Branch-aware routing (automatic)

On a feature branch the entry lands in
`docs/decisions-branches/<sanitized-branch>.md`. On PR merge a workflow
appends a one-line summary linking the PR + the per-branch file to
`docs/decisions.md`. You do not manage this — `logmind log` does.

## Reading prior context

Before starting non-trivial work, read in order:

1. **`docs/timeline.md`** — auto-generated chronological overview across
   every branch; start here.
2. **`docs/decisions.md`** — direct-on-main decisions in detail (20 most
   recent).
3. **`docs/decisions-branches/<your-branch>.md`** if present — decisions
   made earlier on the same feature branch.
4. **`docs/file-structure.md`** — current project tree.

```bash
logmind show               # recent decisions on the current branch
logmind show --all         # include archive
logmind search "postgres"  # full-text across both files
```

## Verifying install health

`logmind doctor` (v0.2.4+) reports installed-vs-latest versions for logmind
(and clud-bug if present), flags stale workflow templates against bundled
markers, and exits non-zero on drift so it's CI-pluggable.

```bash
logmind doctor             # one-shot status table
logmind doctor --json      # for scripts
logmind doctor --offline   # skip PyPI / npm probes
logmind doctor --exit-zero # always exit 0 (informational CI runs)
```

Run after `logmind init` to confirm a clean install, or any time
templates / pins might have drifted. Stale-pin drift is auto-healed by
`logmind init` itself in v0.2.5+, even when no template body changed.

## Setup (one-time, per project)

If the project doesn't yet have logmind:

```bash
pip install logmind
logmind init               # scaffolds docs/, AGENTS.md, GH Actions, .gitignore block
logmind doctor             # confirm clean install
```

## Don'ts

- **Don't use `git add` + `git commit` directly** for changes that carry a
  decision. `logmind log` writes the decision file, stages the touched
  files, and creates the commit in one step. Manual `git commit` either
  bypasses the logging entirely or splits it across two commits — both
  lose the value of having the reasoning attached to the code change.
- **Don't run `logmind timeline --write docs/timeline.md` after a `logmind
  log`** — it's already regenerated and staged. The standalone command is
  an escape hatch for unusual situations (a corrupted timeline, a tree
  someone touched outside logmind), not part of the normal flow.
- Don't log every tiny edit. The 20-line rule is a guideline; use judgement.
- Don't write the decision after the fact in past tense for trivial code.
- Don't reword a decision someone else already logged — link or extend it.
- Don't bypass the auto-commit (`--no-commit`) unless you know the project's
  branch protection requires it.
