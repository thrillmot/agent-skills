## 2026-05-29 23:31 - feat: PR-C3 spacing skills (spacing-system, component-sizing)

**Reasoning:** Phase C / PR-C3 of the UDTS / token.design rollout. spacing-system codifies the two-unit primitive model (minor + major, major divisible by minor; default 4+8 balanced, 2+4 dense, 4+16 spacious) and the derivation chain (padding / gap / icon / height / radius all derive from the units). component-sizing codifies the curated 5-rung height ladders per density, the curated icon-size ladder (12/16/24/32/40/48), and the WCAG 2.5.5 24-px interactive floor (xs is non-interactive in balanced/dense)

**Alternatives considered:** ship spacing as a single skill — rejected, the two have different triggers (designing the unit primitives vs picking a control height); component-sizing depends on spacing-system for the unit primitive context. formula-derive component heights and icon sizes — rejected, the curated values render cleanly while formula values (h-7, h-9, h-11) create indistinct rungs and icon-rendering glitches

**Implications:**
- 2 new SKILL.md files under skills/. Baselines run: spacing-system baseline picked 4 px with exponential ladder but didn't codify the two-unit primitive model or the per-density ladders or WCAG 2.5.5 floor; component-sizing baseline got the 24 px floor right but cited Apple HIG instead of WCAG and formula-derived font/icon pairings instead of curating. After PR-C3 merges, PR-C4 (token-format) becomes the next batch.

---
## 2026-05-29 23:39 - fix: WCAG 2.5.5 → 2.5.8 (Target Size AA); radius-md divergence at spacious not dense; YAML block scalar for component-sizing description

**Reasoning:** clud-bug found three issues: WCAG 2.5.5 is the AAA Target Size (Enhanced) at 44 px — the AA 24 px floor is actually 2.5.8 Target Size (Minimum) added in WCAG 2.2; radius-md note said 'diverges at dense' but it equals sm at both dense and balanced, only diverges at spacious; the component-sizing description had a colon inside parens that YAML treated as a mapping, failing validate-skills

**Alternatives considered:** leave colon and ask validate-skills to relax — rejected, the validator catches a real YAML issue, fix is trivial

**Implications:**
- 2.5.5 → 2.5.8 across both files (8 occurrences); radius-md note corrected; component-sizing description uses YAML literal block scalar; clud-bug threads resolved

---
