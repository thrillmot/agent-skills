## 2026-05-29 23:24 - feat: PR-C2 typography skills (type-scale, line-height-grid)

**Reasoning:** Phase C / PR-C2 of the UDTS / token.design rollout. Each skill encodes one typography algorithm from the foundations/typography.md spec. type-scale codifies the canonical preset ratios (1.067–1.618), the −2 to +8 stops range, the rounding rule (round px first, derive rem from rounded value), and the 1.5 upper bound for product UI. line-height-grid codifies the two-track model (lh-ui = ceil(font × 1.20) + snap; lh-prose = ceil(font × 1.50) + snap) with worked examples for 4/8, 2/4, 4/16 grids

**Alternatives considered:** ship typography as a single mega-skill — rejected, type-scale and line-height-grid have different triggers (designing the scale vs picking line-heights for an existing scale); separating lets agents load only what they need

**Implications:**
- 2 new SKILL.md files under skills/. Baselines run: type-scale baseline picked 1.200 reasonably but didn't codify the full preset table or the rounding-first-then-derive-rem rule; line-height-grid baseline picked sensible ranges (1.10–1.25 ui, 1.45–1.55 prose) but UDTS specifies exactly 1.20 / 1.50. Skills cross-reference each other and back to oklch-color-space (none — typography is orthogonal to color) and forward to spacing-system (PR-C3 dependency on minor-unit). After PR-C2 merges, PR-C3 (spacing) becomes unblocked.

---
