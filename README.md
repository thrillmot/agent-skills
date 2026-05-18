# thrillmot/agent-skills

A collection of [skills.sh](https://www.skills.sh)-compatible agent skills published by [thrillmot](https://thrillmot.com).

These skills run inside any agent that loads skills.sh — Claude Code, Cursor, Codex, Cline, Continue, Aider, Windsurf, and others. Each skill is a single `SKILL.md` file with YAML frontmatter declaring when and how the agent should load it.

## Skills in this collection

| Skill | Purpose |
|---|---|
| [`logmind`](skills/logmind/SKILL.md) | Teach agents when and how to log architectural decisions in projects using [logmind](https://logmind.dev). Activates whenever an agent works in a project with `.logmind/config.yml` or an `AGENTS.md`/`CLAUDE.md` mentioning logmind. |
| [`critical-issues-only`](skills/critical-issues-only/SKILL.md) | PR review discipline — flag only correctness, security, and performance issues. Skip style nits and naming preferences. Ships as a baseline with [clud-bug](https://github.com/thrillmot/clud-bug). |
| [`evidence-based-review`](skills/evidence-based-review/SKILL.md) | Every PR review claim must quote the specific code being criticized. No hand-waving, no vague "might cause issues." Cite or delete. Ships as a baseline with clud-bug. |
| [`respect-existing-conventions`](skills/respect-existing-conventions/SKILL.md) | A code review is not a redesign. Don't suggest changes that fight the codebase's established patterns. Match what's already there. Ships as a baseline with clud-bug. |
| [`clud-bug-collaboration`](skills/clud-bug-collaboration/SKILL.md) | How Claude Code agents working in a clud-bug-installed repo coexist with the bot's review threads, strict-mode gate, and skill set. Activates in any repo with a `clud-bug-review` workflow installed — even if the user didn't mention clud-bug by name. |

## Install

Pick a single skill:

```bash
npx skills add https://github.com/thrillmot/agent-skills --skill logmind
```

Or install the whole collection:

```bash
npx skills add https://github.com/thrillmot/agent-skills
```

Browse on skills.sh: <https://www.skills.sh/thrillmot/agent-skills>

## Consumer patterns — using these skills from your tool

There are two distinct ways a tool can consume a SKILL.md:

**1. Install-pointer (e.g. [logmind](https://logmind.dev)).** The tool ships an `AGENTS.md` that points at the install URL. The *agent runtime* (Claude Code, Cursor, Codex…) installs the skill via `npx skills add`. The tool itself never reads `SKILL.md` — the skill is consumed by the agent, not by the tool. Lightest integration, no runtime fetch.

**2. Fetch + cache + bundled fallback (e.g. [clud-bug](https://github.com/thrillmot/clud-bug)).** The tool itself loads the SKILL.md text into a prompt at runtime — e.g. a bot reviewer needs the skill contents as context for the LLM call. The recommended shape:

```text
1. Try fetching from https://raw.githubusercontent.com/thrillmot/agent-skills/main/skills/<name>/SKILL.md
2. On any failure (network, 404, timeout, non-200) fall back to the bundled copy
   shipped inside the tool's npm/PyPI/etc. package
3. Cache successful fetches to ~/.cache/<tool>/skills/<sha-of-source>.md
4. A scheduled CI job in your tool's repo pulls the bundled copies from
   agent-skills periodically so the offline fallback doesn't drift
```

The bundled copies are the source of truth for the offline path; this repo is the source of truth for the canonical content. Both stay in sync via the scheduled refresh.

Whichever pattern applies, prefer the **collection layout** (`skills/<name>/SKILL.md`) over flat top-level files — that's what skills.sh requires to render the rich page (install count, related skills, security audits).

## Adding a new skill to this collection

1. Create `skills/<name>/SKILL.md` with frontmatter (`name`, `description`).
2. Add a row to the table above.
3. Open a PR.

## License

MIT.
