## 2026-05-29 23:03 - feat: PR-C1 color algorithm skills (oklch-color-space, apca-contrast, wcag-contrast, chroma-harmonization, palette-relationships)

**Reasoning:** Phase C of the UDTS / token.design rollout: each skill encodes one algorithm from the foundations/color.md spec so any agent doing color work in any project can install it. Authored via superpowers:writing-skills RED-GREEN-REFACTOR (subagent baselines run for each skill; key gaps observed: baseline agents default to WCAG over APCA, pick-and-test instead of inverse composition, use Tailwind/Radix semantic names instead of UDTS hue-angle naming, reinvent chroma harmonization without crediting Evil Martians, conflate tetradic-square with tetradic-rectangle)

**Alternatives considered:** ship reusable skills as a 5-PR sequence per-skill — rejected, batched by topic family for review-token economy. ship them inside the tokenomics repo as project-internal — rejected, they're reusable across any color-using project and belong in thrillmade/agent-skills so npx skills add finds them. defer writing skills until APCA standardizes — rejected, the skill cites the APCA exploratory status; UDTS uses APCA + WCAG cross-check today

**Implications:**
- 5 new SKILL.md files under skills/. Each names its trigger surface (When to use / When NOT to use), cross-references siblings via 'REQUIRED BACKGROUND:' phrasing per superpowers:writing-skills convention, includes a verification step, cites sources. Skills cross-link: oklch ← apca, wcag, chroma-harmonization, palette-relationships; apca ↔ wcag; chroma-harmonization references oklch + apca + palette-relationships. After this PR merges (PR-C1), PR-C2 (typography) and PR-C3 (spacing) become unblocked.

---
