# AGENTS.md

This is the canonical instruction file for AI coding agents working in this
repository. Tools that understand `AGENTS.md` (Cursor, Codex, Windsurf,
Claude Code, Cline, Continue, Aider, ...) read this file directly. Per-tool
files like `CLAUDE.md` or `.cursorrules` are stubs that point here so the
guidance lives in one place.

<!-- logmind-start -->
<!-- logmind-block-version: v5-slim -->
## Decision logging — `logmind log` is the commit primitive

This project uses [logmind](https://logmind.dev) for decision logging. **`logmind log` replaces `git add`, `git commit`, and `git push`** for any change that carries a decision. Do not run those git commands directly when logging a decision — `logmind log` does all three in one step.

```bash
logmind log "decision summary" -r "why" -a "alternative" -i "implication"
```

That single command:

1. Writes the decision file (default branch → `docs/decisions.md`, feature branch → `docs/decisions-branches/<branch>.md`).
2. Regenerates `docs/timeline.md` (and `file-structure.md` on the default branch).
3. **`git add`** every change in the working tree (default `--stage all` since v0.2.7).
4. **`git commit`** with message `logmind: <decision>`.
5. **`git push`** to remote.

That's it. No follow-up `git add`, no follow-up `git commit`, no follow-up `git push`, no follow-up `logmind timeline --write`.

### When NOT to use `logmind log`

- **The change has no decision worth recording** (typo fix, formatting, dependency-only patch bump) — use plain `git commit` then.
- **You have unrelated WIP in the working tree** you don't want to commit — pass `--stage scoped` to stage only the decision files (rare for automated agents; they should be working on one thing at a time).

### The full skill

The complete procedure (when something counts as a decision, branch routing, doctor, refresh patterns) lives in the **`logmind` agent skill** at https://github.com/thrillmade/agent-skills/tree/main/skills/logmind. Most agent runtimes auto-load it. If yours doesn't:

```bash
npx skills add https://github.com/thrillmade/agent-skills --skill logmind
```

### Required reading before non-trivial work

1. **[docs/timeline.md](docs/timeline.md)** — chronological overview across all branches; start here.
2. **[docs/decisions.md](docs/decisions.md)** — recent decisions on the default branch.
3. **`docs/decisions-branches/<your-branch>.md`** if present — earlier decisions on this branch.
4. **[docs/file-structure.md](docs/file-structure.md)** — current project tree.

```bash
logmind show               # recent decisions on the current branch
logmind search "keyword"   # full-text across recent + archive
logmind doctor             # check installed version + workflow template drift
```

The team has likely already decided things you'd otherwise re-litigate.
<!-- logmind-end -->

## Project Overview

<!-- Replace with a short description of what this project does. -->

## Development Commands

<!-- Common commands a contributor needs (build, test, lint, run). -->

<!-- clud-bug-start -->
<!-- clud-bug-block-version: v1 -->
## clud-bug — Claude PR review

This repository uses [clud-bug](https://cludbug.dev) to review pull requests
automatically. Three things matter when other agents (or future-you) work
in this repo:

### When you push fixes addressing prior Clud Bug review threads

The bot resolves its own prior review threads on the next pass when it can
verify the fix in the diff. You don't need to manually resolve threads it
opened — push the fix, wait ~2 minutes, and check the PR. If a thread it
left isn't auto-resolved after a fix, the bot judged the issue still open;
read its latest review comment for what it's still flagging.

### Strict mode

Strict mode is **on** in this repo (workflow check fails on critical findings). Toggle by editing `.claude/skills/.clud-bug.json`:

```json
{ "strictMode": true | false, ... }
```

The setting is read from the **base ref** of any open PR, so PRs cannot
disable strict mode on themselves. Changes take effect on PRs opened after
they merge to the base branch.

### Where the skills live

Project-aware review rules live in `.claude/skills/<name>/SKILL.md`. A
small baseline kit ships with every install — see
`.claude/skills/.clud-bug.json` for the current set. Add more via
`clud-bug add <source/name>` (from skills.sh) or by dropping your own
`.md` files there. They auto-load into the reviewer.

### Editing the workflow

Anthropic's `claude-code-action` refuses to run on PRs that modify its own
workflow file. Use `clud-bug edit-workflow` to bundle workflow tweaks into
their own isolated PR — see [README](https://github.com/thrillmade/clud-bug#when-you-edit-the-workflow).

_Installed at clud-bug v0.5.16._
<!-- clud-bug-end -->
