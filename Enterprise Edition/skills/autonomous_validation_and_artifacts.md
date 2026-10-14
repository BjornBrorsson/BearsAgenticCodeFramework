# Skill: Autonomous Validation, Self-Healing Test Loop & Human Verification Artifacts (Enterprise Edition)

> **Load When:** Operating in `phases/phase_test_and_validate.md` or `phases/phase_finalize.md` — spinning up local services, running self-healing test iterations, and creating `./Verification/` artifacts for human review.

---

## 1. Archive Prior `./Verification/` Runs First
Before executing a new validation pass, move any existing files in `./tasks/<task>/Verification/` into `./tasks/<task>/Verification/archive/` with a `YYYYMMDD_HHMMSS_` prefix. Never delete prior verification evidence.

---

## 2. Template: `./Verification/SELF_HEALING_TEST_LOG.md`
Log every failure discovered during autonomous testing here before fixing and re-running:

```markdown
# Autonomous Self-Healing Test Log — `[COMPONENT] — [TASK_ID]`

| Iteration | Timestamp | Boundary | Status | Root Cause Identified | Fix Applied |
|---|---|---|---|---|---|
| #1 | YYYY-MM-DD HH:MM | Happy Path | ❌ FAIL | Case-sensitive key lookup returned `null` | Switched to case-insensitive lookup (`~L42`) |
| #2 | YYYY-MM-DD HH:MM | All 4 Boundaries | ✅ PASS | All assertions, DB counts, and log severities verified | Ready for human review |
```

---

## 3. Template: `./Verification/00_HUMAN_VERIFICATION_GUIDE.md`

```markdown
# Human Verification Guide — `[COMPONENT] — [TASK_ID]`

| # | Test Boundary | Input | Expected | Observed | Evidence File | Status |
|---|---|---|---|---|---|---|
| 1 | **Happy Path** | Standard valid payload | Processed + `[INFO]` logged | Verified | `01_happy_path.log` | ✅ PASS |
| 2 | **Empty / 0-Row Input** | Empty dataset | Clean exit + `[WARNING]` logged | Verified | `02_zero_rows.log` | ✅ PASS |
| 3 | **Boundary & UTF-8** | Negative amount, `ÅÄÖ€` | Handled accurately | Verified | `03_boundary.json` | ✅ PASS |
| 4 | **Error & Rollback** | Invalid input | `try-catch` + `[ERROR]` + DB rollback | Verified | `04_error_rollback.log` | ✅ PASS |

## 2-Minute Human Spot-Check Recipe
1. Inspect `./Verification/01_happy_path.log`.
2. Run `<exact test command or read-only SELECT query>`.
```
