## 2026-05-28 11:36 - clud-bug-collaboration skill update: cover v0.6.3–v0.6.11 cost-control wiring + CLUD_BUG_QUIET + new comment format

**Reasoning:** Plan companion section called for updating clud-bug-collaboration skill with: (1) severity-prefix + <details> comment format from v0.6.5, (2) stats-header pattern + zero-findings case, (3) CLUD_BUG_QUIET=1 env var from v0.6.7. Expanded scope to also document the cost-control wiring agents may wonder about: prompt caching (v0.6.3), per-section byte budgets (v0.6.4), max-turns + MAX_THINKING_TOKENS (v0.6.8), Sonnet 4.6 model pin (v0.6.11), incremental-diff on fix-push + last-reviewed-sha marker (v0.6.10). Three new sections inserted before 'Updating clud-bug itself': Reading review comments, Agent-invocation mode, Why reviews are cheap. SKILL.md went from 126 lines to ~190 lines — still well within reasonable size.

---
