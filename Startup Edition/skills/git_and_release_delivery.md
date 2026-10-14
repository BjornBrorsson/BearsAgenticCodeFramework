# Skill: Git Hygiene, Release Delivery & Lightweight Runbooks (Startup Edition)

> **Load When:** Preparing a commit/release, cleaning up scratch files, or handing over an operational job/script.

---

## 1. Keep Git Clean (`./src/` vs. `./tasks/`)
* Production code goes in `./src/`; throwaway scripts, test dumps, and `Verification/` logs stay in `./tasks/<feature>/`.
* Ensure `.gitignore` excludes `.env`, `*.log`, `*.tmp`, `*.bak`, and transient build outputs.
* Commit or stash before large refactors so you always have a clean rollback point.

## 2. Lightweight Handover & Runbook Template
When finishing a feature, job, or migration, include a brief summary:
* **Files Changed & Purpose**
* **How to Run / Verify:** Commands and `./tasks/<feature>/Verification/` evidence
* **Deployment Order & Rollback:** (e.g. "1. Run migration → 2. Deploy app; rollback via `git revert` + rollback SQL")
* **Operational Notes (for cron jobs/webhooks):** Schedule/trigger, required `.env` keys, log location, and failure recovery.
