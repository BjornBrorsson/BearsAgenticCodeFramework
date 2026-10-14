# Phase: TEST & VALIDATE — Local Spin-Up, Self-Healing Loop & Human Verification Artifacts (Startup Edition)

> **Gatekeeper:** Read this file in full when testing and validating what has been built, spinning up local dev servers, self-healing test failures, and generating `./Verification/` proof for human review.
> **Companion Skills:** Load `../skills/autonomous_validation_and_artifacts.md` and `../skills/local_testing.md`.

---

## 5-Step Autonomous Validation Loop

1. **Spin Up & Mute Dev Comms:** Ensure local servers/DB are running and `.env`/dev configs have outbound email, SMS, webhooks, and live payments muted (`0`/`false`/test mode).
2. **Archive Older Runs:** Move any previous files in `./tasks/<feature>/Verification/` into `./tasks/<feature>/Verification/archive/`.
3. **Run the 4-Boundary Matrix:** Execute build, linter, tests, and live flow across: (1) Happy Path, (2) Empty/0-Row/Null Input, (3) Edge/Boundary/UTF-8 Input (`å, ä, ö, €`), and (4) Error/Unauthorized Path.
4. **Self-Healing Loop (Report Bugs to Yourself):** If any check fails, log it in `./Verification/SELF_HEALING_TEST_LOG.md` (Symptom → Root Cause → Fix), fix the code, and re-run from scratch until 100% green.
5. **Create Human Verification Package:** Write `./Verification/00_HUMAN_VERIFICATION_GUIDE.md` with the PASS/FAIL summary, before/after state proof, self-healed bug notes, and a **1-Minute Human Spot-Check Recipe**.
