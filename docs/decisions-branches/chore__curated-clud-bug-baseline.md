## 2026-05-27 15:40 - Add skill-frontmatter-quality + exclude clud-bug-collaboration from this repo's clud-bug baseline

**Reasoning:** agent-skills IS the skill catalog, so clud-bug-collaboration's 'how to coexist with the bot' guidance doesn't apply to its own PRs (no upstream-bot relationship to manage). Drop from the curated baseline via excludedBaselines (new manifest field in clud-bug v0.6.1) and add skill-frontmatter-quality so SKILL.md PRs get qualitative review on description quality, trigger surface, voice, and review_mode completeness — the layer above validate-skills.yml's mechanical name/H1/required-field checks. First production use of the excludedBaselines field.

**Alternatives considered:** Patch clud-bug to drop clud-bug-collaboration from baseline globally — rejected: other consumer repos legitimately want the skill, so exclusion belongs in this repo's manifest, not clud-bug's bundle., Skip authoring skill-frontmatter-quality, only do the exclusion — rejected: clud-bug-review needs a replacement reviewer or PR review coverage thins. The qualitative-quality layer is what made dropping the meta-skill worth doing., Use kind:remote in installed[] instead of excludedBaselines — rejected: 'installed' is for skills clud-bug manages; excluded baselines aren't managed, they're suppressed. Different concept, separate field.

**Implications:**
- skill-frontmatter-quality is dedicated-mode (consistent with brand-voice-review / pii-and-compliance / api-contract-enforcement / test-discipline). Will load in its own focused Claude call on SKILL.md PRs.
- First production use of excludedBaselines field. If it misbehaves, the bug surfaces on next clud-bug update (re-creates the mirror dir we just deleted). Recovery: delete dir again, file clud-bug issue.
- Mirror at .claude/skills/skill-frontmatter-quality/SKILL.md kept byte-identical to source per the existing baseline-mirror pattern. Verified via diff -q after edit.

---
