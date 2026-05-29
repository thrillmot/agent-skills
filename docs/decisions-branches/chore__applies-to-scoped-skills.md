## 2026-05-29 10:15 - 0.0.K companion: add applies_to to brand-voice-review, api-contract-enforcement, test-discipline

**Reasoning:** Companion to clud-bug#113 (0.0.K). With applies_to landing in clud-bug v0.6.21, scoped skills can declare path/extension globs that gate body fetch. Three skills get applies_to blocks: brand-voice-review (UI paths + .tsx/.jsx/.vue/.svelte/.html/.md/.mdx extensions), api-contract-enforcement (api/routes/handlers paths + .proto/.graphql/.thrift extensions), test-discipline (test/spec paths + .test.* / .spec.* / _test.* extensions). Each skill's existing description already telegraphs the right scope — the applies_to block makes it executable. The fourth dedicated skill, pii-and-compliance, INTENTIONALLY stays un-scoped because PII can leak from any code path that emits data outward (logs, telemetry, external API calls), and the false-negative cost of skipping it on a PR with hidden logging would be high. Cost of universal-apply for PII: one frontmatter scan.

**Alternatives considered:** Add applies_to to pii-and-compliance with logging/telemetry/db globs. Rejected: PII leaks frequently happen inside unrelated code paths (a backend handler that suddenly starts logging a user object). Better to always-apply for compliance-critical skills until per-line filtering is feasible.

**Implications:**
- Once clud-bug#113 (v0.6.21) is published, consumers running clud-bug update will pick up the new prompt instruction. From there, agents loading these skills via clud-bug-review will only fetch matching skill bodies — measurable savings will appear in 'clud-bug usage' output ($/LOC will drop on PRs that don't match dedicated skills).

---
