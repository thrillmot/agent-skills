## 2026-05-29 16:26 - Upgrade agent-skills to clud-bug v0.6.23 (adaptive --max-turns)

**Reasoning:** v0.6.23 ships Phase 0.5 §5: paths-check pre-flight computes a max_turns bucket (10/15/25/40) based on file count + prior thread count, forwarding it as --max-turns to claude-code-action. Plus actions: read permission moved to the clud-bug-review job (was on paths-check in v0.6.22, but per-job GITHUB_TOKEN permissions aren't inherited).

**Alternatives considered:** Wait + bundle v0.6.23 + future v0.6.24 — rejected: tokenomics PR #18 unblock workflow demonstrated v0.6.23 stabilises consumer-side reviews

**Implications:**
- AGENTS.md marker bumped to v0.6.23. App-side workflow-self-modification guard fires once (admin-bypass) per documented per-PR-checklist structural exception.

---
