# Phase 6: REVIEW & SIGN-OFF — Code Audit, Spec Traceability, Drift Check & Formal Sign-Off Gate (Enterprise Edition)

> **Gatekeeper:** Read this file in full when asked to review, audit, or sign off on code, SQL scripts, configurations, PRs, or release candidates.

---

## 1. The 7-Point Enterprise Audit & Sign-Off Protocol

1. **Spec & Acceptance Traceability:** Every requirement and `- [ ]` criterion in `[TASK_ID]_Specs.md` / `Tickets/` is implemented and backed by a proof file in `./Verification/`.
2. **Security, Privacy & Compliance (`../skills/security_privacy_and_compliance.md`):** Zero hardcoded secrets; strict tenant/client isolation; non-prod comms muted (`0`/`false`); parameterized SQL and validated inputs.
3. **Database & Transaction Integrity (`../skills/database_change_scripts.md`):** Schema matches `SCHEMA_INDEX.md`; DML/DDL scripts include `SET XACT_ABORT ON`, `BEGIN TRANSACTION`, pre/post `SELECT` checks, row-count ceiling guards, `IF NOT EXISTS` idempotency, and a companion **Rollback Script**.
4. **Architecture & Code Quality:** Reuses existing patterns ("less code is better"), follows `../skills/conventions_and_naming.md`, comments explain the *why*, and Durable Deep Links are up to date.
5. **Robustness, Pre-Flight & Logging:** Validates file existence, 0-byte inputs, permissions, and DB/API reachability; wraps I/O in `try-catch`; logs `[YYYY-MM-DD HH:MM:SS] [SEVERITY] [Component] - Message`.
6. **Branch / Environment Drift Check:** Compares `./tasks/<task>/` drafts against `dev`/`staging`/`release` without overwriting target branches unprompted.
7. **Verification & Runbook Readiness:** Includes `./Verification/00_HUMAN_VERIFICATION_GUIDE.md`, Deployment Manifest, and Operational Runbook.

---

## 2. Formal Sign-Off Verdict & Report Template

Conclude every review with an explicit verdict:
* **`🟢 APPROVED FOR STAGING / RELEASE`** — Zero blockers; all 7 dimensions and `./Verification/` proof verified.
* **`🟡 APPROVED WITH CONDITIONS`** — No safety/data blockers; minor non-blocking items noted for staging verification.
* **`🔴 CHANGES REQUIRED (BLOCKED)`** — Safety, schema, logic, or verification blockers found.

```markdown
## Code Review & Sign-Off Report: `[COMPONENT] — [TASK_ID]`
- **Sign-Off Verdict:** `[🟢 APPROVED | 🟡 APPROVED WITH CONDITIONS | 🔴 CHANGES REQUIRED]`
- **Files Audited:** `<paths>`
- **Verification Package:** `./tasks/<task>/Verification/00_HUMAN_VERIFICATION_GUIDE.md`

| # | Severity | File & Symbol (`~Line`) | Finding & Risk | Required Fix |
|---|---|---|---|---|
| 1 | **BLOCKER / WARN / NIT** | `...` (`~L45`) | ... | ... |
```
