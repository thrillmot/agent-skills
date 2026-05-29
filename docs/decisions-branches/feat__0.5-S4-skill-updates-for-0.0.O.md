## 2026-05-29 16:39 - Update clud-bug-collaboration + logmind skills for Phase 0.5 / 0.0.O (§4)

**Reasoning:** Two skills in this repo carry agent-facing collaboration guidance that v0.6.22 changed: (1) clud-bug-collaboration MUST now describe the bot-identity contract (claude[bot] for inline threads, github-actions[bot] for the summary comment under GITHUB_TOKEN), the --json-schema structured-output flow, the fallback to bare H2 when schema validation exhausts retries, the bot-login override consuming workflows must pass to strict-mode-gate, and the status_header: bare default for non-strict-mode repos. (2) logmind/SKILL.md needs an explicit 'Token cost: git vs logmind log' subsection — Phase 0.5 user-raised concern that agents running git add/commit/push instead of logmind log are net spenders. The bench provides the numbers; the skill needs to make the heuristic explicit so agents don't fall back to git by default.

**Alternatives considered:** Defer §4b.1 token-cost subsection to a future logmind-skill rev — rejected: the bench numbers are already shipping in v0.5.7; pin the heuristic now while propagation is fresh, Bundle the AGENTS.md.slim.template promotion (§4b.2) into this PR — rejected: that lives in logmind repo, not agent-skills; will ship as a separate logmind PR after #81 lands

**Implications:**
- 5 new sections in clud-bug-collaboration/SKILL.md (~100 lines). 1 new subsection in logmind/SKILL.md (~30 lines). Companion §4b.2 (AGENTS.md.slim.template logmind-block lead-line promotion) NOT in this PR — separate logmind PR. Total diff: small, +130 lines across 2 skill files.

---
