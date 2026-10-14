# Phase 3: FINALIZE — VERIFY → DELIVER → RECORD → REPORT (Enterprise Edition)

> **Gatekeeper:** Read this file in full **before** executing verification runs, archiving `./Verification/` evidence, updating logs/memories/wiki, preparing deployment manifests, or declaring a task "Completed".
> **Hard Rule:** "Done" means **verified and recorded**, never merely written.

---

## 1. VERIFY — Testing, Dry-Runs & Verification Artifacts

### 1.1 No Fake or Placeholder Tests
* **Never** write placeholder assertions (`assert(true)`, `expect(1).toBe(1)`, or comment-only tests).
* Every test must execute real production logic and assert concrete outputs across:
  1. **Happy Path** (valid inputs → expected state/output)
  2. **Null / Empty / 0-Row Inputs** (empty result sets log `[WARNING]` and exit cleanly; missing optional fields handled)
  3. **Boundaries & Oversized Inputs** (max lengths, large batches, negative/zero amounts, UTF-8/special characters, injection strings, timeouts)
  4. **Permission Blocks & Error Paths** (unauthorized callers rejected; exceptions caught and logged as `[ERROR]`)

### 1.2 Local Dry-Runs & Transaction-Safe DB Tests
* Exercise the change end-to-end in a local/sandbox instance (`../skills/local_testing.md`) and inspect the real logs.
* When a local database test must mutate data, **always** wrap it in a rollback transaction so the database remains unchanged:
  ```sql
  SET XACT_ABORT ON;
  BEGIN TRANSACTION;
    -- 1. Pre-check counts/state
    -- 2. Execute test logic; inspect row counts and timing
    -- 3. Assert post-check counts/state
  ROLLBACK TRANSACTION; -- leaves database completely unchanged
  ```

### 1.3 Verification Artifacts & `./Verification/` Archiving (MANDATORY)
1. Ensure `./tasks/<task>/Verification/` and `./tasks/<task>/Verification/archive/` exist.
2. **Archive Older Runs:** Move any previous verification files from `./Verification/` into `./Verification/archive/` (timestamped) — never delete prior verification evidence.
3. **Save Current Evidence in `./Verification/`:** Store dry-run log outputs, test runner results, before/after SQL query snapshots, and sample output payloads for manual human control.

---

## 2. DELIVER — Staging, Release Manifest & Operational Runbook

*(See `../skills/git_and_release_delivery.md` for full templates.)*
* Deliver complete, copy-paste-ready files.
* For any SQL change script targeting non-local environments, include the mandatory reminder:
  > *"Here is the script. Please review it carefully and execute it yourself."*
* Provide a **Staging & Deployment Manifest** specifying: target paths/branches, deployment order, dependencies, validation evidence (`./Verification/`), and exact **Rollback Recovery Steps**.
* For operational jobs/services/integrations, include a concise **Operational Runbook** (Purpose, Schedule/Trigger, Config Keys, Paths, Log Locations, Rollback).

---

## 3. RECORD — Self-Improving Knowledge Loop

Route every finding to its designated home so the workspace starts smarter next session:

| Discovered / Created Finding | Where It Goes | Scope |
|---|---|---|
| What changed this task, why, verification evidence, post-mortem, rollback plan | `./tasks/<task>/PROJECT_LOG.md` | Task-Specific |
| Edge cases, config notes, or quirks specific to this client/component | `./tasks/<task>/PROJECT_MEMORIES.md` | Component/Client |
| How a feature/domain works + verified Durable Deep Links to source code | `./docs/wiki/<domain>.md` *(anonymized)* | Repo Domain |
| Reusable framework, DB, or API gotcha + fix (Two-Strike or >30 min) | `./docs/MASTER_MEMORIES.md` *(anonymized)* | Repo Global |
| New directory, guide, tool, or architectural entry point | `./docs/DOCUMENTATION_INDEX.md` | Repo Navigation |
| New or modified database table, view, column, or API payload contract | `./docs/schemas/SCHEMA_INDEX.md` | Repo Schema |

* **Kanban Board Sync (if `Tickets/` is active):** `git mv` the ticket from `Tickets/Ongoing/` to `Tickets/Completed/`, set `Status: Completed`, check off all `- [x]` criteria, append final `## Work Log` entry, and update `Tickets/00_Plan.md`.

---

## 4. REPORT — Done Checklist & Final Handover

Verify every item before declaring the task **"Completed"**:

- [ ] Lint, typecheck, compile, and static checks pass completely.
- [ ] Local dry-run succeeded; logs verified for correct `[INFO]` / `[WARNING]` / `[ERROR]` severities.
- [ ] No fake tests; boundary, null/empty, oversized, and failure paths validated.
- [ ] Manual verification artifacts saved in `./Verification/` and older runs archived in `./Verification/archive/`.
- [ ] All code modifications commented with the *why* (intent, invariants, constraints).
- [ ] `./docs/wiki/<domain>.md` created/updated with verified, date-stamped Durable Deep Links (`Symbol` + `~L45` + `_Verified: YYYY-MM-DD_`).
- [ ] `PROJECT_LOG.md` (with post-mortem & rollback plan) and `PROJECT_MEMORIES.md` updated in `./tasks/<task>/`.
- [ ] Reusable gotchas anonymized and promoted to `./docs/MASTER_MEMORIES.md`.
- [ ] Handover includes a complete Deployment Manifest, Rollback Plan, and (if operational) Runbook.
