## 2026-05-29 14:28 - Upgrade agent-skills to clud-bug v0.6.22 + logmind v0.5.6 (Phase 0.5 propagation §1)

**Reasoning:** Phase 0.5 candidates shipped end-to-end this session (clud-bug v0.6.13 → v0.6.22, logmind v0.5.5 → v0.5.6). agent-skills was the first of 4 consuming repos to propagate. clud-bug update --quiet renders v0.6.22 workflow: --json-schema structured output (0.0.O), Render+post step + fallback, paths-check pre-flight job (0.0.W workflow-only skip), bot-login: github-actions[bot] (0.0.O identity contract), bumped strict-mode-gate@v0.6.21 → v0.6.22. logmind agents update --apply refreshes AGENTS.md logmind-block v5-slim → v6-pointer (~69% reduction, 2526 → 841 bytes per read). CLAUDE.md clud-bug-block removed because @AGENTS.md import is present (0.0.I.1 behavior). Per-PR-checklist note: clud-bug-review WILL fail on this PR because of the App-side workflow-self-modification guard (anthropics policy — claude-code-action refuses to run on PRs that modify its own workflow file). Documented structural exception; admin-bypass merge per the checklist.

**Alternatives considered:** Pin to clud-bug@latest (floating tag). Rejected: violates Q1 reproducible-deploy invariant (templates ship as defaults; floating tags would let upstream silently land mid-cycle)., Wait for clud-bug to self-upgrade first (Phase 0.5 §3). Rejected: §3 is sequenced AFTER §1 because §1 validates the recipe on simpler targets before applying to clud-bug (which has more sensitive self-modification surface).

**Implications:**
- After merge: future workflow-only PRs in agent-skills auto-skip the LLM via 0.0.W's paths-check (the structural admin-bypass is one-time per repo). Future content PRs run under v0.6.22's structured-output flow → fewer turns, lower $/LOC, no max-turn-exhaustion risk.
- Block size saving compounds: agent-skills sessions reading AGENTS.md amortize ~1.7 KB/read across every agent run touching this repo.

---
