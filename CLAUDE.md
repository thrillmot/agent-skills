<!-- logmind-stub: AI agent instructions for this project live in AGENTS.md -->
See [AGENTS.md](AGENTS.md) for project-specific AI agent instructions, including
the decision-logging requirement (logmind) and required reading.

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
