## 2026-05-27 11:55 - fix(notify-clud-bug.yml): pipefail-safe the detect grep (zero-matches no-op)

**Reasoning:** Fix: wrap the grep in '{ grep -oE … || true; }' so the pipeline survives the zero-match exit. The empty stdin then propagates through sed/sort/jq, yielding '[]' that the existing handler catches

**Implications:**
- Dormant in production — no baseline SKILL.md has changed since notify-clud-bug.yml's push trigger landed (PR #29), so the failing path never fired. Real-world trip would be a force-push or any push that doesn't touch the 4 tracked baselines despite the path-filter matching
- Verification: smoke-test via workflow_dispatch (already known good) + then an empty commit to one of the 4 baseline paths to exercise the detect-and-handle-zero-match branch

---
