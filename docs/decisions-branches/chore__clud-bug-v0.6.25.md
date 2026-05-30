## 2026-05-29 22:26 - Upgrade agent-skills to clud-bug v0.6.25 (Smart Budget Phase 1)

**Reasoning:** v0.6.25 ships line-based pre-flight estimation + in-prompt budget self-rationing + calibration measurement + workflow concurrency group + issue #89 + gotcha #2. See tokenomics #24 for full design notes.

**Implications:**
- Same propagation pattern: App-guard fires on workflow-modification → admin-bypass once → subsequent workflow-only PRs auto-skip via 0.0.W.

---
