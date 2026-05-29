## 2026-05-29 09:58 - Update token-frugal-tooling skill with Phase 0.5 patterns (0.0.T, 0.0.W, 0.0.R, 0.0.X, 0.0.E)

**Reasoning:** The skill last-revised in PR #46 (token-frugal-tooling meta-skill ship) doesn't yet document the Phase 0.5 wins shipped after v0.6.12. Adds: (1) ### Diagnostics block reading guidance for 0.0.T tee-hint output, (2) four 'Don't worry about...' bullets for 0.0.W workflow-skip, 0.0.R Haiku routing, 0.0.X output brevity directive, 0.0.E golden gate. Net +24 lines, 108 → 132. The skill stays a quick-reference; detail still routes to clud-bug-collaboration.

**Implications:**
- Other agents reading this skill in the next session will know that a ### Diagnostics block in a Clud Bug summary is intentional (not malformed output) and what it signals. They'll also see the v0.6.14-v0.6.20 cost defaults so they don't 'helpfully' override them.

---
