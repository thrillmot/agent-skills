## 2026-05-28 13:14 - chore: clud-bug v0.5.16 → v0.6.12 (Sonnet pin, caching, budgets, brief CLI, incremental-diff, AGENTS trim, self-update YAML fix)

**Reasoning:** First end-to-end propagation of Phase A wins to this consuming repo. Six files changed: clud-bug-{audit,review,self-update}.yml workflows re-rendered to v0.6.12 templates; AGENTS.md + CLAUDE.md block trimmed to v0.6.6 v2 format; .cursorrules updated; .clud-bug.json lastUpdateVersion stamped. Brings in: APPEND_SYSTEM_PROMPT caching, per-section byte budgets (MAX_DIFF/COMMENT/SKILL_BYTES), severity-prefix + collapsible Reasoning comment format, CLUD_BUG_QUIET CLI, --max-turns 15 + MAX_THINKING_TOKENS=8000, Sonnet 4.6 model pin, incremental-diff handshake via last-reviewed-sha marker, fixed self-update.yml.tmpl (workflow_dispatch unblocked).

**Implications:**
- First PR in any consuming repo to be reviewed by clud-bug on the UPDATED v0.6.12 templates after merge. The PR's OWN review still uses the v0.5.16 templates because the workflow on base ref hasn't changed yet (chicken-and-egg). After merge, every future PR in this repo benefits from the new cost-control regime.

---
