## 2026-05-27 07:09 - chore: bump logmind pin 0.3.0 → 0.3.3

**Reasoning:** v0.3.3 specifically fixes the post-merge hook re-staging docs/file-structure.md bug. Bumping the workflow pin lets CI regenerate the timestamp-less file-structure.md on the next push, closing the migration symptom

**Implications:**
- Refresh applied via logmind init's refresh-mode (marker v4-slim already at v5-slim from prior pass; init bumped the workflow pin marker independently)
- Working tree includes docs/file-structure.md regen — that's the v0.3.3 timestamp-drop landing locally, intentional

---
