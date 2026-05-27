## 2026-05-27 07:25 - feat(notify-clud-bug.yml): add push trigger with matrix strategy over changed baselines

**Reasoning:** Path filter at the event level (skills/<each>/SKILL.md) gates ANY trigger; the detect job's diff then narrows to exactly which of the 4 baselines actually changed. matrix.skill replaces inputs.skill_name throughout

**Alternatives considered:** (b) wrapper-workflow-dispatches-main approach: two files, more moving parts. Chose (a) for symmetry with the rest of the workflow's single-file design

**Implications:**
- Force-push edge case handled: if path filter says yes but diff says no (history rewrite), emit a notice and skip with no error
- fail-fast: false in matrix so one skill's transform failure doesn't cancel siblings

---
