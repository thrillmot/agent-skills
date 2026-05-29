## 2026-05-29 16:26 - Upgrade agent-skills to clud-bug v0.6.23 (adaptive --max-turns)

**Reasoning:** v0.6.23 ships Phase 0.5 §5: paths-check pre-flight computes a max_turns bucket (10/15/25/40) based on file count + prior thread count, forwarding it as --max-turns to claude-code-action. Plus actions: read permission moved to the clud-bug-review job (was on paths-check in v0.6.22, but per-job GITHUB_TOKEN permissions aren't inherited).

**Alternatives considered:** Wait + bundle v0.6.23 + future v0.6.24 — rejected: tokenomics PR #18 unblock workflow demonstrated v0.6.23 stabilises consumer-side reviews

**Implications:**
- AGENTS.md marker bumped to v0.6.23. App-side workflow-self-modification guard fires once (admin-bypass) per documented per-PR-checklist structural exception.

---
## 2026-05-29 16:33 - Fix publisher SKILL.md path in AGENTS.md (gotcha #2)

**Reasoning:** agent-skills IS the publisher of clud-bug-collaboration — the skill lives at skills/clud-bug-collaboration/SKILL.md (not the consumer install path .claude/skills/...). clud-bug update doesn't know that and renders the consumer path → check-links fails on AGENTS.md. Documented in plan §1 'Recurring gotchas' as a structural issue to fix in clud-bug v0.6.24 (clud-bug update should detect publisher-of-clud-bug-collaboration and use the local path). For now: manual fix per cycle.

**Implications:**
- This same fix was applied to PR #53 (v0.6.22 propagation). Will repeat every cycle until clud-bug v0.6.24 ships the detector.

---
