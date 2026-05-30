## 2026-05-29 23:03 - feat: PR-C1 color algorithm skills (oklch-color-space, apca-contrast, wcag-contrast, chroma-harmonization, palette-relationships)

**Reasoning:** Phase C of the UDTS / token.design rollout: each skill encodes one algorithm from the foundations/color.md spec so any agent doing color work in any project can install it. Authored via superpowers:writing-skills RED-GREEN-REFACTOR (subagent baselines run for each skill; key gaps observed: baseline agents default to WCAG over APCA, pick-and-test instead of inverse composition, use Tailwind/Radix semantic names instead of UDTS hue-angle naming, reinvent chroma harmonization without crediting Evil Martians, conflate tetradic-square with tetradic-rectangle)

**Alternatives considered:** ship reusable skills as a 5-PR sequence per-skill — rejected, batched by topic family for review-token economy. ship them inside the tokenomics repo as project-internal — rejected, they're reusable across any color-using project and belong in thrillmade/agent-skills so npx skills add finds them. defer writing skills until APCA standardizes — rejected, the skill cites the APCA exploratory status; UDTS uses APCA + WCAG cross-check today

**Implications:**
- 5 new SKILL.md files under skills/. Each names its trigger surface (When to use / When NOT to use), cross-references siblings via 'REQUIRED BACKGROUND:' phrasing per superpowers:writing-skills convention, includes a verification step, cites sources. Skills cross-link: oklch ← apca, wcag, chroma-harmonization, palette-relationships; apca ↔ wcag; chroma-harmonization references oklch + apca + palette-relationships. After this PR merges (PR-C1), PR-C2 (typography) and PR-C3 (spacing) become unblocked.

---
## 2026-05-29 23:12 - fix wcag-contrast: pt vs px thresholds + SC 2.4.13 level

**Reasoning:** clud-bug found two real WCAG-spec errors: (1) SC 2.4.13 Focus Appearance is Level AA in WCAG 2.2 (October 2023), not AAA — the AAA criterion is 2.4.12 Focus Not Obscured (Enhanced). agents following the skill would skip a legally-required AA check; (2) the large-text threshold is in POINTS not pixels — 18 pt regular ≈ 24 CSS px and 14 pt bold ≈ 18.67 CSS px. writing 14 px / 18 px sets the threshold ~25% too low, so text at 15–18 px bold would incorrectly pass at 3:1 when it requires 4.5:1

**Alternatives considered:** leave as-is and let consumers translate — rejected, the whole point of the skill is to give agents the correct threshold; (b) reference WCAG by SC number only and skip the threshold value — rejected, the threshold IS the load-bearing piece of the skill

**Implications:**
- wcag-contrast/SKILL.md table now includes Level column; 1.4.3 rows state thresholds in points with the CSS px conversion alongside; 2.4.13 row moved to AA; new 2.4.12 row added at AAA for the focus-not-obscured criterion. Description and Verification sections updated to mirror

---
