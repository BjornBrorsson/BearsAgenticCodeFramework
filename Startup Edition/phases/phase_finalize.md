# Phase 3: FINALIZE — VERIFY → RECORD → Handover (Startup Edition)

> **Gatekeeper:** Read this file in full before running final verification, updating docs/logs, or declaring a task "Done".
> **Hard Rule:** "Done" means **ran, verified, and recorded** — never just written.

---

## 1. VERIFY — Run It & Prove Boundaries

* **No Fake Tests:** Never write `assert(true)`. Every test must exercise real logic and cover:
  1. **Happy Path**
  2. **Null / Empty / 0-Row Inputs**
  3. **Obvious Edge & Boundary Cases** (large payloads, negative/zero amounts, UTF-8 characters `å, ä, ö`, invalid inputs)
  4. **Failure / Unauthorized Paths**
* **Run It Locally:** Execute the build, linter, test suite, and the actual endpoint/script/UI flow (`../skills/local_testing.md`). Fix any compile, lint, or runtime error before handover.
* **Transaction-Safe DB Tests:** Wrap non-persisted local DB write tests in `BEGIN TRANSACTION … ROLLBACK TRANSACTION`.
* **Verification Artifacts (`./Verification/`):**
  * Save verification outputs (test logs, dry-run outputs, SQL before/after snapshots) in `./tasks/<feature>/Verification/`.
  * Move any older verification files from previous runs into `./tasks/<feature>/Verification/archive/` rather than deleting or overwriting them.

---

## 2. RECORD — Self-Improving Knowledge Base

Route each finding to one home so the next session starts faster:

| What You Learned / Produced | Where It Goes |
|---|---|
| What changed + 1-line post-mortem (if bugfix) + rollback note | `./tasks/<feature>/PROJECT_LOG.md` |
| A quirk or constraint specific to this feature | `./tasks/<feature>/PROJECT_MEMORIES.md` |
| How the feature works + verified Durable Deep Links (`Symbol` + `~L42` + `_Verified: YYYY-MM-DD_`) | `./docs/wiki/<feature>.md` |
| A reusable bug/quirk any feature could hit (Two-Strike or >30 min) | `./docs/MASTER_MEMORIES.md` |
| A new folder, doc, or entry point | `./docs/DOCUMENTATION_INDEX.md` |
| New/updated DB tables or API payloads (if used) | `./docs/schemas/SCHEMA_INDEX.md` |

* **Kanban Board Sync (if `Tickets/` exists):** `git mv` card from `Tickets/Ongoing/` to `Tickets/Completed/`, set `Status: Completed`, check off all `- [x]` boxes, and append a closing `## Work Log` entry.

---

## 3. REPORT — Done Checklist

- [ ] Builds cleanly with zero compile, typecheck, or lint errors.
- [ ] Ran locally; happy path, empty/null inputs, and edge cases work.
- [ ] Verification artifacts saved in `./Verification/` (prior runs moved to `./Verification/archive/`).
- [ ] `./docs/wiki/<feature>.md` created/updated with verified, date-stamped Durable Deep Links.
- [ ] `PROJECT_LOG.md` updated (with post-mortem & rollback note).
- [ ] Any twice-hit or >30-min quirk logged in `./docs/MASTER_MEMORIES.md`.
- [ ] Concise handover note to the user: what changed, verification proof, and how to run/deploy it (plus *"Here is the script. Please review it carefully and execute it yourself."* for any shared/prod SQL script).
