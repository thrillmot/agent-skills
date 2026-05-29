## 2026-05-29 00:59 - chore: add @AGENTS.md import to CLAUDE.md (Q4 refinement / 0.0.I)

**Reasoning:** Adds @AGENTS.md as the first line of CLAUDE.md so Claude Code auto-loads the canonical AGENTS.md content via @-import instead of relying on the in-stub 'see AGENTS.md' redirect. Eliminates the silent failure mode where Claude reads CLAUDE.md, sees the stub, and (for whatever reason) doesn't follow the redirect. Token cost: SAME as today if Claude was following the redirect; FIXES the silent failure mode. Single source of truth preserved (AGENTS.md stays canonical). Cross-tool unchanged (Cursor/Codex/Aider read AGENTS.md directly). Cross-platform OK (Claude Code's @-syntax, not a filesystem symlink). Trivial 1-line addition.

---
