# Phase 5: DEBUG — Root-Cause Diagnosis & Reproduction (Startup Edition)

> **Gatekeeper:** Read this file in full when debugging a bug, investigating a failing test/endpoint, or analyzing logs.

---

## Diagnostic Loop

1. **Check `./docs/MASTER_MEMORIES.md` & `./docs/wiki/<feature>.md` First:** See if this quirk is already solved.
2. **Trace Logs & State:** Read the real error stack trace and inspect DB state (using non-locking `SELECT` queries and checking `SCHEMA_INDEX.md`/models first).
3. **Compare Surfaces (if API/Integration bug):** Check whether the bug is in the API route/serialization layer or in the underlying domain function (see `../skills/api_and_contract_testing.md`).
4. **Reproduce Before Fixing:** Trigger the bug locally or in a test and save the failing output in `./tasks/<feature>/Verification/before_fix_repro.log`.
5. **Fix Root Cause & Record:** Fix in `phase_build.md`, verify in `phase_finalize.md`, and if it took >30 minutes or could recur, log it in `./docs/MASTER_MEMORIES.md`.
