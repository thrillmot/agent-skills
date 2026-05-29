## 2026-05-29 14:28 - Upgrade agent-skills to clud-bug v0.6.22 + logmind v0.5.6 (Phase 0.5 propagation §1)

**Reasoning:** Phase 0.5 candidates shipped end-to-end this session (clud-bug v0.6.13 → v0.6.22, logmind v0.5.5 → v0.5.6). agent-skills was the first of 4 consuming repos to propagate. clud-bug update --quiet renders v0.6.22 workflow: --json-schema structured output (0.0.O), Render+post step + fallback, paths-check pre-flight job (0.0.W workflow-only skip), bot-login: github-actions[bot] (0.0.O identity contract), bumped strict-mode-gate@v0.6.21 → v0.6.22. logmind agents update --apply refreshes AGENTS.md logmind-block v5-slim → v6-pointer (~69% reduction, 2526 → 841 bytes per read). CLAUDE.md clud-bug-block removed because @AGENTS.md import is present (0.0.I.1 behavior). Per-PR-checklist note: clud-bug-review WILL fail on this PR because of the App-side workflow-self-modification guard (anthropics policy — claude-code-action refuses to run on PRs that modify its own workflow file). Documented structural exception; admin-bypass merge per the checklist.

**Alternatives considered:** Pin to clud-bug@latest (floating tag). Rejected: violates Q1 reproducible-deploy invariant (templates ship as defaults; floating tags would let upstream silently land mid-cycle)., Wait for clud-bug to self-upgrade first (Phase 0.5 §3). Rejected: §3 is sequenced AFTER §1 because §1 validates the recipe on simpler targets before applying to clud-bug (which has more sensitive self-modification surface).

**Implications:**
- After merge: future workflow-only PRs in agent-skills auto-skip the LLM via 0.0.W's paths-check (the structural admin-bypass is one-time per repo). Future content PRs run under v0.6.22's structured-output flow → fewer turns, lower $/LOC, no max-turn-exhaustion risk.
- Block size saving compounds: agent-skills sessions reading AGENTS.md amortize ~1.7 KB/read across every agent run touching this repo.

---
## 2026-05-29 14:32 - Fix two CI failures on PR #53: bump logmind workflow pin 0.3.3 → 0.5.6 + fix SKILL.md path overwritten by clud-bug update

**Reasoning:** Two failures beyond the expected App-guard: (1) check-derived-docs failed because .github/workflows/regen-timeline.yml + check-doc-links.yml were pinned at logmind==0.3.3 (pre-brief-format era). CI's old logmind regenerated timeline.md in v0.3.3's verbose format and saw drift vs my locally-committed v0.5.6 brief format. Ran logmind init --no-git which is the documented refresh path (bumps the workflow's template-version marker + rewrites the install pins to current logmind). Now both workflows pip install logmind==0.5.6, matching what I produce locally. (2) check-links failed because clud-bug update --quiet overwrote AGENTS.md's clud-bug-collaboration skill reference, replacing the agent-skills-specific path 'skills/clud-bug-collaboration/SKILL.md' with the consumer-install path '.claude/skills/clud-bug-collaboration/SKILL.md'. agent-skills IS the publisher of clud-bug-collaboration, not a consumer — its copy lives at skills/, not .claude/skills/. Restored the publisher-path. Same fix as agent-skills#48 which originally caught this.

**Alternatives considered:** Patch clud-bug-update to detect 'this repo is the publisher of clud-bug-collaboration' (e.g. via skills/clud-bug-collaboration/SKILL.md existing) and skip block install OR use the local path. Better long-term fix but requires a clud-bug v0.6.23 release; this PR fixes the symptom in agent-skills only., Add agent-skills' AGENTS.md to a 'do not clud-bug-update this file' list in clud-bug. Rejected: agent-skills DOES want the clud-bug-version marker bumped on AGENTS.md; just not the skill-path overwritten. Surgical exception is hard to express via skip-list.

**Implications:**
- Future clud-bug update runs in agent-skills will overwrite the SKILL.md path again. Track as a known recurring issue until clud-bug ships a publisher-aware fix. Workaround: re-apply the path patch each propagation cycle, OR add a post-update hook in agent-skills.
- Future propagation PRs to consumer repos (NOT publishers): the consumer-install path '.claude/skills/clud-bug-collaboration/SKILL.md' is CORRECT for them — they install the skill there via skills.sh. The path-fix is publisher-specific (only agent-skills hits this).

---
