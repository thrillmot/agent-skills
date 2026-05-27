## 2026-05-27 00:49 - Add review_mode: shared to all baseline-type skill frontmatters

**Reasoning:** clud-bug bundled copies inject review_mode: shared into the 3 baseline reviewer skills' frontmatters (clud-bug-collaboration, critical-issues-only, evidence-based-review, respect-existing-conventions). Bundled copies diverge from agent-skills source on that single line, making notify-clud-bug.yml (the future PR 2b mechanical sync) harder than necessary — a pure 'cp source to bundled' would clobber clud-bug's enrichment. Adding the field at source eliminates the divergence and makes the sync a verbatim copy. Same field added for logmind so the catalog is consistent across all skills (logmind ships as a baseline elsewhere in some installs too).

**Alternatives considered:** Strip review_mode from bundled copies on clud-bug side instead — rejected: the field is real routing config that clud-bug consumes; stripping breaks the routing semantics., Skip logmind (different lifecycle) — rejected: consistency across the catalog is cheap and the field is the right default everywhere except the 4 already-dedicated skills., Mark test-discipline as shared too — rejected: existing file has it as dedicated, and per 'respect-existing-conventions' (this very skill in the same PR), match the convention. Test review benefits from focused attention as much as brand voice / PII / API contract.

**Implications:**
- Five frontmatters touched: logmind, critical-issues-only, evidence-based-review, respect-existing-conventions, clud-bug-collaboration. Four already-dedicated skills (api-contract-enforcement, brand-voice-review, pii-and-compliance, test-discipline) unchanged. validate-skills.yml passes additional fields through; no validator change needed.
- Unblocks PR 2b — notify-clud-bug.yml can be a 'cp source → bundled + bump SHA + bump version + open PR' shell script with no transform layer or LLM in the middle.
- Future skills authored in this catalog should declare review_mode explicitly; the existing inconsistency (where 4 had it and 5 didn't) was an accident of authoring order, not intent.

---
