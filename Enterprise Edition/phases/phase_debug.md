# Phase 5: DEBUG — Root-Cause Diagnosis, Log Analysis & Reproduction (Enterprise Edition)

> **Gatekeeper:** Read this file in full whenever troubleshooting a defect, investigating a failed job/integration, analyzing logs, or triaging an API/contract discrepancy.
> **Core Rule:** Reproduce or prove the exact failure path in logs/data/code before writing a fix.

---

## 1. The 5-Step Diagnostic Loop

```text
1. CHECK MEMORIES/WIKI → 2. TRACE LOGS & DATA → 3. COMPARE SURFACES → 4. REPRODUCE → 5. FIX & ADD REGRESSION TEST
```

### Step 1 — Check `./docs/MASTER_MEMORIES.md` & `./docs/wiki/` First
Check whether the symptom matches a known framework, database, encoding, or API gotcha already recorded in `MASTER_MEMORIES.md` or the domain wiki page.

### Step 2 — Trace Logs, Code & Database State Safely
* Inspect application/job logs for `[ERROR]`/`[WARNING]` entries, correlation IDs, and stack traces.
* Check `./docs/schemas/SCHEMA_INDEX.md` before querying any table.
* Run **SELECT-only, non-locking** diagnostic queries (`WITH (NOLOCK)` / `READ UNCOMMITTED`) to inspect current data state.
* Trace the full call chain: `API / UI / Scheduler → Controller / Handler → Domain Service → DB Table / External Integration`.

### Step 3 — Compare API vs. Internal Handler vs. Schema Contract
When diagnosing an API or integration failure (see `../skills/api_and_contract_testing.md`):
1. Compare observed API behavior against the declared schema (OpenAPI, XSD, DB schema) AND against the internal/non-API handler for the same operation:
   * **API differs from internal handler** → **API / Adapter Bug**.
   * **Both behave identically** → **Core logic or possibly intended behavior** (raise a rulings question `RQ-NNN` or confirm with the user before changing semantics).
2. Search the issue tracker for existing open/closed reports before filing a duplicate defect.

### Step 4 — Reproduce Before Fixing
* Create a failing test case or local reproduction script (`BEGIN TRANSACTION … ROLLBACK` for DB probes).
* Save the failing output in `./tasks/<task>/Verification/before_fix_repro.log`.

### Step 5 — Fix Root Cause & Record
* Transition to `phase_build.md` to fix the root cause, then `phase_finalize.md` to prove the reproduction now passes.
* **Two-Strike Rule:** If diagnosing the issue took >30 minutes or uncovered a reusable platform quirk, promote it to `./docs/MASTER_MEMORIES.md`.
