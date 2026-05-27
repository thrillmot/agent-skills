## 2026-05-27 00:43 - Add .github/dependabot.yml for github-actions ecosystem only

**Reasoning:** Workflow files reference pinned action versions (actions/checkout@v6, etc). Without dependabot, these drift silently and CI breaks when a major action retires its runtime — same trap logmind/clud-bug both hit pre-dependabot. github-actions-only scope: this repo has no code dependencies (markdown skills + a small Python validator with no third-party deps), so no npm/pip ecosystem needed.

**Alternatives considered:** Add npm + pip ecosystems too — rejected: nothing in repo to update. Would generate empty Dependabot output., Wait for the planned thrillmade/reporulez/templates/dependabot/github-actions-only.yml template — rejected: doesn't exist yet, and the config is 5 lines. If reporulez ships a different shape later, we can reconcile in a one-line diff.

**Implications:**
- Weekly cadence picked to align with logmind/clud-bug dogfood schedules and avoid daily PR noise on a low-churn repo.
- Future: if we add a Node tool (e.g., for a SKILL.md preview/render), add an npm entry then.

---
