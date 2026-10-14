# Skill: Autonomous Validation, Self-Healing Loop & Human Verification Artifacts (Startup Edition)

> **Load When:** Operating in `phases/phase_test_and_validate.md` or `phases/phase_finalize.md` — spinning up local servers, fixing test failures autonomously, and creating `./Verification/` proof.

---

## 1. Archive Older Runs
Move any prior files in `./tasks/<feature>/Verification/` to `./tasks/<feature>/Verification/archive/` (`YYYYMMDD_HHMMSS_...`) before running new tests.

## 2. Self-Healing Log (`./Verification/SELF_HEALING_TEST_LOG.md`)
Whenever a test or dry-run fails, log `Iteration | Boundary | Failure | Root Cause | Fix Applied`, fix the bug, and re-run until all 4 boundaries pass.

## 3. Human Verification Guide (`./Verification/00_HUMAN_VERIFICATION_GUIDE.md`)
```markdown
# Verification Guide — `<feature>`
| Boundary | Input | Outcome | Evidence | Status |
|---|---|---|---|---|
| 1. Happy Path | Valid input | Expected output + `[INFO]` | `01_happy.log` | ✅ PASS |
| 2. Empty / 0-Row | Empty input | Clean exit + `[WARNING]` | `02_empty.log` | ✅ PASS |
| 3. Edge / UTF-8 | `ÅÄÖ€`, `-100` | Handled cleanly | `03_edge.json` | ✅ PASS |
| 4. Error Path | Invalid input | Caught + `[ERROR]` logged | `04_error.log` | ✅ PASS |

## 1-Minute Human Spot-Check
Run: `<exact command, URL, or query to verify in 60 seconds>`
```
