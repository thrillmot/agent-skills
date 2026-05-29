---
name: clud-bug-collaboration
description: How Claude Code agents working in a clud-bug-installed repo should interact with the bot's review threads, strict-mode gate, and skill set. Use this skill whenever you're about to push a commit, address a clud-bug PR review comment, edit anything under .claude/skills/, modify .github/workflows/clud-bug-*.yml, or wonder why a PR check is red. Also use when planning work in a repo that has a `clud-bug-review` workflow installed — even if the user didn't mention clud-bug by name.
review_mode: shared
---

# Working in a clud-bug-installed repo

Clud Bug reviews every PR via `anthropics/claude-code-action`. As another
Claude Code agent working alongside it, here's what to know — these aren't
arbitrary rules, they're the consequences of how the gate is wired.

## When you push fixes to a clud-bug-reviewed PR

The bot reviews on `pull_request: synchronize` (every push). When you push
a commit that addresses an issue Clud Bug flagged in a prior review, the bot
will re-review the PR within ~2 minutes and **resolve its own prior
unresolved inline review threads** where the flagged issue is verifiably
fixed in the current diff. You don't have to resolve threads manually.

If a thread it left isn't auto-resolved after your fix:
- The bot judged the issue still open (read its latest comment for why).
- Or the resolution call hit a transient API issue (re-push to retry).

Don't manually resolve clud-bug threads on its behalf. The check
(`required_conversation_resolution` branch protection) will block merge if
unresolved threads remain — and the right fix is usually "fix the actual
issue and re-push," not "mark the conversation resolved."

## When you read the PR's `clud-bug-review` check status

- **Green** = Clud Bug ran and either found no critical issues OR strict
  mode is off (advisory).
- **Red** = either the action errored OR strict mode is on AND Clud Bug
  flagged a critical issue. Read the latest `## 🐛 Clud Bug review` comment
  on the PR — the body indicates "critical findings" or "clean."
- **Skipped/green-with-comment** = bot- or fork-authored PR (Dependabot,
  Renovate, fork contributor). GitHub deliberately doesn't pass repo
  secrets to those workflows. The bot posts a one-line "Clud Bug skipped"
  comment and exits 0. Review the diff manually.

## Strict mode

Read `.claude/skills/.clud-bug.json` to check this repo's setting.
`strictMode: true` means the workflow check fails on critical findings.
The setting is read from the **base ref**, so a PR cannot disable strict
mode on itself by editing the manifest. Changes take effect for PRs opened
after the change merges to the base branch.

To disable strict mode for a repo, edit the manifest on the base branch:

```json
{ "strictMode": false, ... }
```

## When you modify a clud-bug skill

Skills live in `.claude/skills/<slug>/SKILL.md`. Three groups:

- **Baseline** (`critical-issues-only`, `evidence-based-review`,
  `respect-existing-conventions`) — managed by clud-bug; if you edit them
  in-place they'll be overwritten on the next `clud-bug update`. To
  customize behavior for this repo, write a NEW skill rather than mutating
  a baseline.
- **From skills.sh** — installed via `clud-bug add <source/name>`. Tracked
  in `.claude/skills/.clud-bug.json`. Use `clud-bug refresh` to sync.
- **Custom** (anything not in the manifest) — yours, never touched by any
  clud-bug command. Drop a new `.md` here and it auto-loads on the next PR.

When you write a custom skill, follow the SKILL.md frontmatter format
(`name`, `description`) and write specific, evidence-anchored guidance.
Generic advice gets ignored; rules with examples and quoted-line evidence
move the bot's behavior.

## When you edit `.github/workflows/clud-bug-*.yml`

`anthropics/claude-code-action` **refuses to run on PRs that modify its
own workflow file** (App token exchange fails with 401, "Workflow
validation failed"). This is a security guard against PRs that try to
neuter the reviewer or exfiltrate secrets.

Consequence: if you bundle a workflow tweak with other work, the
`clud-bug-review` check on that PR will fail and not actually review your
other changes either.

The fix: split workflow edits into their own PR. The CLI helps:

```bash
# After editing .github/workflows/clud-bug-*.yml locally:
clud-bug edit-workflow
```

It refuses to run if your working tree has non-workflow changes, so the
isolation guarantee is enforced. Branches from `origin/main`, not HEAD —
unrelated commits on a feature branch can't leak in.

## When the secret is missing

`ANTHROPIC_API_KEY` must be set in the repo's Actions secrets. Without it,
the workflow's guard step fails loudly with an `::error::` annotation
explaining how to set it. (For bot/fork PRs where the secret legitimately
isn't passed, the guard posts a one-line advisory comment and exits 0
instead of failing red.)

## Bot identity contract (v0.6.22+)

Two different bot accounts post to a clud-bug PR — knowing which is
which matters when you grep / filter prior comments:

- **`claude[bot]`** authors **inline review threads** (every 🔴 / 🟡
  finding posts here). Authentication: composite-action exchanges
  the `claude-code-oauth-token` for a short-lived App token; comments
  post under the App's identity.
- **`github-actions[bot]`** authors the **summary comment** (the
  `## 🐛 Clud Bug review` H2 block with the stats line + collapsed
  per-skill scan + per-section findings). Authentication: workflow
  post-step uses the workflow's own `GITHUB_TOKEN`, which posts under
  `github-actions[bot]`.

When you scan prior comments to decide what was already said, two
endpoints carry distinct comment types — and within each, you may
encounter either bot identity:

```bash
# Issue-level / summary thread (the H2 review-summary comment lives
# here, authored by github-actions[bot]). Agents posting follow-ups
# at the issue level also show up here.
gh api "repos/$REPO/issues/$PR/comments" --jq '
  [.[] | select(.user.login == "github-actions[bot]" or .user.login == "claude[bot]")]
  | sort_by(.created_at)
'

# Inline review threads (each 🔴 / 🟡 finding lives here, authored by
# claude[bot] via the MCP tool). This endpoint never contains the
# summary comment; that's issue-level.
gh api "repos/$REPO/pulls/$PR/comments" --jq '
  [.[] | select(.user.login == "claude[bot]")]
  | sort_by(.created_at)
'

# GraphQL reviewThreads — same inline data as `pulls/$PR/comments`
# but grouped into threads, and exposes `isResolved`. Use this when
# you need to resolve threads programmatically after a fix.
gh api graphql -f query='{ repository(...) { pullRequest(...) { reviewThreads(...) { ... } } } }'
```

Pitfall: don't conflate the two endpoints. `issues/$PR/comments`
never returns inline findings (they aren't issue-level comments);
`pulls/$PR/comments` never returns the H2 summary (it's issue-level,
not anchored to a diff line). A complete picture of "what did the
bot say already?" reads both.

## Structured output schema (v0.6.22+ / 0.0.O)

The summary comment is no longer free-form prose. The LLM emits a
JSON object matching the workflow-defined `--json-schema` (see
`templates/workflow.yml.tmpl`); the workflow post-step renders that
JSON to GitHub-flavored markdown.

What this means for collaborating agents:

- The shape is **stable**. Stats counts, per-skill scan rows, per-
  finding objects (`skill`, `file`, `line`, `summary`, `reasoning`)
  always appear in the same order; the H2 header always matches the
  documented enum (`critical findings` / `clean` / `bare`).
- Inline review threads still post one-per-finding via the MCP tool
  `mcp__github_inline_comment__create_inline_comment` — those are
  NOT in the structured output. The structured object describes them
  (for the renderer's collapsed scan + counts), but the actual
  GitHub inline-comment placement is the MCP tool's job.
- The structured output also carries `last_reviewed_sha` — the HEAD
  SHA at review time, used by the incremental-diff handshake
  (v0.6.10) on the next fix-push. The renderer embeds it as
  `<!-- last-reviewed-sha: <sha> -->` in the summary body.

## Fallback when structured output is empty

If `claude-code-action` retried schema validation up to its limit
and never produced a valid object, `structured_output` arrives at
the post-step empty. The post-step then posts a **bare-H2 advisory
comment** (no body, just `## 🐛 Clud Bug review`) and exits 0.

- Strict-mode gate falls open in this case — the gate post-step
  greps for `## 🐛 Clud Bug review` and the bare header matches, so
  the check turns green ADVISORY (not red). Q6 invariant: missing
  signal must not block merge silently.
- The job-summary panel surfaces the fallback so an operator can
  see why no findings rendered. Look for "structured-output empty;
  fell back to bare H2" if you find a PR where the bot ran but
  emitted nothing.

## `bot-login` override (v0.6.22+)

Each consuming workflow passes `bot-login: 'github-actions[bot]'`
to the `strict-mode-gate` composite action. The composite's default
is still `claude[bot]` (pre-v0.6.22 back-compat), but every freshly-
rendered consumer workflow overrides to `github-actions[bot]` because
THAT is now the identity that authors the H2 summary the gate
inspects.

If you write a custom workflow that calls the gate directly, set
`bot-login: 'github-actions[bot]'` — using the composite default
will silently fail to find the summary on v0.6.22+ posts.

## `status_header: "bare"` (v0.6.22+ default)

Non-strict-mode repos (the default — check
`.claude/skills/.clud-bug.json` for `strictMode: true`) emit
`status_header: "bare"` in the structured output. The renderer
produces `## 🐛 Clud Bug review` with no suffix, matching the v0.6.21-
behaviour exactly. Strict-mode repos emit `critical findings` or
`clean` and the renderer appends `— critical findings` / `— clean`.

The strict-mode-gate post-step greps for "critical findings" to
decide red-or-green; "bare" + "clean" both fall through to green.

## Reading review comments (v0.6.5+ format)

Since v0.6.5, every Clud Bug summary comment leads with a stats line for
fast triage:

```
Found: N 🔴 / N 🟡 / N 🟣
```

Three severity tiers, applied per finding via emoji prefix:

- **🔴 important** — bug, security, performance, missing test coverage. In
  strict-mode repos these fail the check.
- **🟡 nit** — style, naming, micro-optimization. Advisory.
- **🟣 pre-existing** — issue that pre-dates this PR. Don't fix here unless
  the user asks; flag for a follow-up issue.

Each finding follows this shape:

```
🔴 [skill-name]: One-line claim anchored to file:line.
<details><summary>Reasoning</summary>

Longer explanation with quoted evidence.

</details>
```

Re-reads (other agents picking up the PR) can skip the collapsed `Reasoning`
block when they trust the headline; expand only when chasing a finding.
GitHub renders the `<details>` natively for human readers. **If a review
emits zero findings**, the entire comment is just the `Found: 0 🔴 / 0 🟡 / 0 🟣`
stats line plus the standard summary header — no per-finding bullets.

## Agent-invocation mode: `CLUD_BUG_QUIET=1` (v0.6.7+)

When invoking the `clud-bug` CLI from an agent session (vs interactively),
set `CLUD_BUG_QUIET=1` or pass `--quiet` / `-q` to suppress multi-line
progress chatter. Each command emits a single `ok <key-value>` summary line
so the agent's context stays lean. Errors still print on stderr.

```bash
CLUD_BUG_QUIET=1 clud-bug update
# → ok updated: @v0.6.11, N changed, M unchanged

CLUD_BUG_QUIET=1 clud-bug init
# → ok initialized: .claude/skills/ N specimens, workflow @v0.6.11

CLUD_BUG_QUIET=1 clud-bug add evidence-based-review
# → ok added: .claude/skills/evidence-based-review/SKILL.md
```

## Why reviews are cheap (cost-control wiring, v0.6.3–v0.6.11)

The bot is engineered for token frugality, several layers deep:

- **Prompt caching** (v0.6.3): the stable review-prompt + skill catalog +
  AGENTS.md from base ref land in the Claude Code CLI's auto-cached system
  layer. Cached input is billed at 10% of standard input within a 5-minute
  window. The first review in a fresh window writes the cache (1.25×); the
  next ones in that window read at 10%. Visible via `cache_read_input_tokens`
  in the run's result JSON (`show_full_output: true`).
- **Per-section byte budgets** (v0.6.4): `MAX_DIFF_BYTES=80000`,
  `MAX_COMMENT_BYTES=20000`, `MAX_SKILL_BYTES=4000`. Caps the PR diff /
  prior-comment / skill-content section that the bot ingests on each run.
  When a section hits its cap, an explicit truncation marker appears and
  the bot is instructed to request the omitted hunks if relevant.
- **`MAX_THINKING_TOKENS=8000`** + **`--max-turns 15`** (v0.6.8): thinking
  budget + agentic-loop ceiling. Stops runaway turn-storms.
- **Model pin** (v0.6.11): clud-bug-review now runs on
  `claude-sonnet-4-6`, not Opus. Per Anthropic docs: "Sonnet handles most
  coding tasks well and costs less than Opus." ~80% per-token reduction vs
  Opus 4.7 default.
- **Incremental-diff on fix-push** (v0.6.10): on a re-review, the bot reads
  only the delta since its prior pass (`git diff <prior_sha>..HEAD`),
  identified via a `<!-- last-reviewed-sha: <sha> -->` HTML marker in the
  prior summary comment. Force-push or rebase invalidates the ancestry
  check and falls back to full `gh pr diff`. ~67% fewer bytes ingested
  across a fix-push-heavy PR's lifetime.

You don't need to invoke any of this — it's wired into every clud-bug
template. Override per-repo (e.g., `MAX_DIFF_BYTES=999999`) by setting the
env var in your consuming repo's `clud-bug-review.yml`, which is visible in
PRs and clud-bug-reviewable.

## Updating clud-bug itself

`clud-bug-self-update.yml` runs weekly (Mondays 12:00 UTC) and opens a PR
when a newer clud-bug version is published to npm. Pin to a specific
version by adding `pinVersion: "x.y.z"` to `.claude/skills/.clud-bug.json`.

To trigger an update on demand:

```bash
clud-bug update
```

Re-renders workflow templates and refreshes baseline skills from the
currently-installed clud-bug version. Custom and skills.sh-installed
skills are left untouched.

## Where to find more

- Site: https://cludbug.dev
- Repo: https://github.com/thrillmade/clud-bug
- Skill catalog: https://github.com/thrillmade/agent-skills
