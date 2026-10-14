# Phase 4: HOTFIX — Urgent Fix Overlay (Startup Edition)

> **Gatekeeper:** Read this file **IN ADDITION TO** `phase_research.md`, `phase_build.md`, and `phase_finalize.md` when handling an urgent production bug. Compresses ceremony — never bypasses safety or verification.

---

## 1. Triage First
State the **blast radius** up front (what users, data, or payments are affected) and whether a quick rollback or feature flag can stabilize production immediately.

## 2. What May Be Compressed
* Reuse an existing `./tasks/<feature>/` folder.
* Jump straight to `./docs/MASTER_MEMORIES.md` and the feature's `./docs/wiki/<feature>.md` page before coding.

## 3. What Is Never Compressed
* **Safety (§1):** Never run destructive SQL directly on a shared/production DB — deliver a transaction-wrapped `.sql` script with pre/post checks and rollback for the user to run. Never hardcode secrets or enable real outbound comms in dev.
* **Smallest Reversible Fix:** Fix only the broken bug; do not bundle refactors.
* **Verify & Record:** Run the fix locally, save proof in `./Verification/`, and log the root cause + prevention in `PROJECT_LOG.md` (and `MASTER_MEMORIES.md` if reusable).
