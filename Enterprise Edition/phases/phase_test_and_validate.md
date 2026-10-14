# Phase: TEST & VALIDATE — Local Spin-Up, Self-Healing Test Loop & Human Verification Artifacts (Enterprise Edition)

> **Gatekeeper:** Read this file in full whenever validating what has been built, spinning up local services, running the self-healing test loop, or creating `./Verification/` artifacts for human review.
> **Companion Skills:** Load `../skills/autonomous_validation_and_artifacts.md` and `../skills/local_testing.md`.

---

## 1. The 5-Step Validation & Self-Healing Loop

1. **Pre-Flight & Local Environment Spin-Up:**
   * Confirm outbound email, SMS, webhooks, and payment gateways are muted (`0` / `false` / mock) in local configs.
   * Check and spin up required local services, containers, or databases and verify readiness.
2. **Archive Prior `./Verification/` Runs:**
   * Move any older files in `./tasks/<task>/Verification/` into `./tasks/<task>/Verification/archive/` (timestamped) — never delete prior verification runs.
3. **Execute the 4-Boundary Test Matrix:**
   Run the real build, automated tests, and end-to-end dry-run across:
   * **Happy Path**
   * **Empty / 0-Row / Null Input** (verify `[WARNING]` log and clean exit)
   * **Boundary, Sign & UTF-8 Edge Cases** (negative/zero amounts, max lengths, special characters `å, ä, ö, €`)
   * **Error, Permission & Rollback Path** (verify `try-catch`, `[ERROR]` log with stack trace, and `BEGIN TRAN … ROLLBACK` DB safety)
4. **Self-Reporting & Self-Healing Loop (Report Bugs to Yourself):**
   * Whenever a test, log check, or row-count check fails, log an iteration entry in `./Verification/SELF_HEALING_TEST_LOG.md` (Symptom → Root Cause → Fix Applied), fix the code, and re-run from scratch until 100% green.
5. **Package Human Verification Artifacts (`./Verification/`):**
   * Generate `./Verification/00_HUMAN_VERIFICATION_GUIDE.md` containing the PASS/FAIL matrix, before/after DB proof, self-healed bug summary, and a **2-Minute Human Spot-Check Recipe** alongside the raw log/output files.
