## 2026-05-27 00:49 - Add review_mode: shared to all baseline-type skill frontmatters

**Reasoning:** clud-bug bundled copies inject review_mode: shared into the 3 baseline reviewer skills' frontmatters (clud-bug-collaboration, critical-issues-only, evidence-based-review, respect-existing-conventions). Bundled copies diverge from agent-skills source on that single line, making notify-clud-bug.yml (the future PR 2b mechanical sync) harder than necessary — a pure 'cp source to bundled' would clobber clud-bug's enrichment. Adding the field at source eliminates the divergence and makes the sync a verbatim copy. Same field added for logmind so the catalog is consistent across all skills (logmind ships as a baseline elsewhere in some installs too).

**Alternatives considered:** Strip review_mode from bundled copies on clud-bug side instead — rejected: the field is real routing config that clud-bug consumes; stripping breaks the routing semantics., Skip logmind (different lifecycle) — rejected: consistency across the catalog is cheap and the field is the right default everywhere except the 4 already-dedicated skills., Mark test-discipline as shared too — rejected: existing file has it as dedicated, and per 'respect-existing-conventions' (this very skill in the same PR), match the convention. Test review benefits from focused attention as much as brand voice / PII / API contract.

**Implications:**
- Five frontmatters touched: logmind, critical-issues-only, evidence-based-review, respect-existing-conventions, clud-bug-collaboration. Four already-dedicated skills (api-contract-enforcement, brand-voice-review, pii-and-compliance, test-discipline) unchanged. validate-skills.yml passes additional fields through; no validator change needed.
- Unblocks PR 2b — notify-clud-bug.yml can be a 'cp source → bundled + bump SHA + bump version + open PR' shell script with no transform layer or LLM in the middle.
- Future skills authored in this catalog should declare review_mode explicitly; the existing inconsistency (where 4 had it and 5 didn't) was an accident of authoring order, not intent.

---
## 2026-05-27 06:10 - Rewrite notify-clud-bug.yml as mechanical PR-opener (workflow_dispatch only)

**Reasoning:** Original v0.x workflow opened an issue on thrillmade/clud-bug saying 'baseline skill X changed, bump BASELINE_SKILLS_REF + sync bundled copy + release'. The maintainer then applied those steps by hand. The new version applies them directly: copies source SKILL.md to bundled, bumps the SHA pin, bumps patch version, prepends CHANGELOG entry, opens PR. PR shape eliminates a manual round-trip; the PR can be reviewed, merged, and released in one pass instead of issue→branch→push→PR→merge→release. PR 2a already eliminated the source/bundled frontmatter divergence (review_mode field) so the copy is verbatim — no transform layer or LLM in the loop.

**Alternatives considered:** Open a PR auto on push to baseline SKILL.md — rejected (initially): workflow_dispatch only during smoke-test phase, so the produced PRs can be manually inspected for shape correctness before granting auto-trigger authority. Add 'on: push' as a one-line follow-up after the first 2-3 manual runs look right., Sync ALL changed baselines in a single PR — rejected: skill-by-skill PRs keep clud-bug-review's per-PR context tight, isolate any sed/awk misfires to one skill, and give the maintainer skill-level control over which to land first., Call Anthropic API to draft the bundled copy from a diff — rejected (the original plan's default): hallucination surface for a fundamentally mechanical task. PR 2a (review_mode source/bundled alignment) made this unnecessary.

**Implications:**
- Idempotency via branch name (skills-refresh/<skill>-<short-sha>) — re-running for the same source SHA finds an open PR and exits silently. Different SHA → different branch → new PR. Stale PRs from prior runs stay open for manual cleanup; acceptable since the maintainer was going to look at them anyway.
- CLUD_BUG_NOTIFY_PAT now needs Contents + Pull requests: write on thrillmade/clud-bug (previously only Issues: write). Token must be rotated before the first dispatch — the workflow exits non-zero with a clear error if the scope is missing, so failure mode is loud.
- Side-effects asserted by step 'Apply sync': sed regex match on the BASELINE_SKILLS_REF line is verified by grep after; if the constant's declaration shape ever changes the workflow fails with a specific error rather than silently no-opping. node used for package.json edits (no jq dep).

---
