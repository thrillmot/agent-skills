## 2026-05-29 23:46 - feat: PR-C4 token-format skills (design-token-naming, dtcg-format, semver-design-tokens)

**Reasoning:** Phase C / PR-C4 of the UDTS / token.design rollout. design-token-naming codifies Spec-PR-B's hyphen-prefix convention where the prefix declares class (contrast-bound vs free) and kind, with the class redundantly encoded in $extensions.udts.class — the load-bearing convention for AI-legible token catalogs. dtcg-format covers the W3C DTCG spec ($type/$value/$extensions, aliases, groups, composite tokens) plus the UDTS-specific $extensions.udts schema. semver-design-tokens codifies the auto-compute bump policy (major = remove/rename/type-change/class-change; minor = add; patch = value-only or alias-preserving-value), the resolved-values-not-source-paths rule, pre-1.0 relaxation (removals → minor), and the deprecation cycle

**Alternatives considered:** ship as a single mega-skill — rejected, the three have very different triggers (designing names vs authoring DTCG vs picking a version bump); separating lets agents load only the relevant one

**Implications:**
- 3 new SKILL.md files. design-token-naming baseline proposed dot-separated paths (e.g. color.primary.surface.default.rest) — fundamentally different from UDTS's hyphen-prefix convention; SKILL.md corrects to prefix-loaded names with class encoded. dtcg-format baseline was strong (got $type/$value/$extensions, aliases, groups, alias-cycles) but didn't cover the UDTS schema; SKILL.md adds the catalog-level mode-axis and per-token shapes. semver-design-tokens baseline was strong; SKILL.md adds UDTS's auto-compute approach + snapshot storage convention. After PR-C4 merges, PR-C5 (web-interface-guidelines-review, the last batch) becomes unblocked.

---
