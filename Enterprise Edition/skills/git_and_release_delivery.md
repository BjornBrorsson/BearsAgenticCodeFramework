# Skill: Git Staging, Release Delivery & Operational Runbooks (Enterprise Edition)

> **Load When:** Promoting finalized artifacts from `./tasks/<task>/` into the repository or staging branch, handling environment drift (`dev` / `staging` / `release`), or authoring a Deployment Manifest and Operational Runbook.

---

## 1. Workflow Boundary: Task Workspace vs. Protected Branches

* **Drafting Happens in `./tasks/<task>/`:** Keep scratch scripts, draft specs, verification logs, and unverified prototypes inside `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/`.
* **Never Modify Read-Only Core or Release Snapshots Unprompted:**
  * Protected core directories (`./src/core/` or `[CORE_LIBRARY_PATH]`) and release/package snapshots (`dev`/`staging`/`release`/`pkg`) must never be overwritten or "aligned" without explicit user permission.
  * Older environment snapshots often drift from active development (e.g. a bumped version header on an older gated body). Do not treat drifted snapshots as the source of truth or auto-align them unless instructed.

---

## 2. Staging & Release Promotion Protocol

1. **Verify Locally First (`phase_finalize.md`):** Confirm all static checks, tests, and local dry-runs pass and evidence is saved in `./tasks/<task>/Verification/`.
2. **Stage Only Committed, Clean Artifacts:**
   * Exclude transient files (`*.log`, `*.tmp`, `temp/`, `archive/`, `*.bak`, `.env`).
   * Verify `git status` and `git diff` to ensure no unintended edits or secrets are staged.
3. **Preview Before Publishing:** Always run a dry-run diff/status check and present the publication plan (`[ADD]`, `[MODIFY]`, `[DELETE]`) to the user before pushing or publishing to upstream/shared branches.

---

## 3. Required Delivery Manifest & Operational Runbook Template

Include this manifest in `[TASK_ID]_Specs.md`, `PROJECT_LOG.md`, and the final handover report:

```markdown
## Delivery & Deployment Manifest — `[PREFIX]_[TASK_ID]`

| Order | Artifact | Source Path | Target Destination Path | Action | Dependencies |
|---|---|---|---|---|---|
| 1 | `[PREFIX]_[TASK_ID]_Script_Migration.sql` | `./tasks/.../script/` | `./src/db/migrations/...` | ADD | Run manually before service deploy |
| 2 | `<Service / Module File>` | `./src/...` | `./src/...` | MOD | Requires Step 1 |

### Validation Evidence
- Dry-run logs, test outputs & SQL before/after counts: `./tasks/<task>/Verification/`

### Rollback & Recovery Notes
1. Revert code commit / redeploy prior artifact version.
2. Execute companion rollback SQL block in `[PREFIX]_[TASK_ID]_Script_Migration.sql`.

---

## Operational Runbook (For Jobs / Services / Integrations)
- **Purpose:** <What the job/service does>
- **Schedule / Trigger:** <Cron / scheduler / event / webhook / manual CLI>
- **Config Keys & Feature Flags:** <`[PREFIX]__ParamName` / env vars and defaults>
- **Input / Output Paths:** <Directories, queues, or endpoints>
- **Log Location & Monitoring:** <Log path; expected `[INFO]`, `[WARNING]`, `[ERROR]` lines>
- **Rollback / Failure Recovery:** <How to safely re-run or roll back a failed batch>
```
