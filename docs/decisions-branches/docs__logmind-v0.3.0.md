## 2026-05-27 00:27 - Document logmind v0.3.0 merge-driver surface in skills/logmind/SKILL.md

**Reasoning:** Notifier issue #17 was open since v0.3.0 (2026-05-26). v0.3.0 added a real install-time surface (.gitattributes block, per-clone git config, post-merge hook, file-structure --write CLI, 3 new doctor rows) that agents reading doctor output or rebasing parallel-PR branches will encounter — not internal-only, so a no-op close would leave the skill drifted.

**Alternatives considered:** Close #17 as no-op (rejected: v0.3.0 is user-visible, not refactor), Split into 3 separate PRs per concept (rejected: one cohesive 'parallel-PR merges' section reads as one idea)

**Implications:**
- Skill grows ~35 lines; structure now has a v0.3.0 section sitting between Verifying-install-health and Upgrading sections
- Next refresh likely on v0.4.0 (notify-clud-bug pattern, per separate plan)

---
