# Phase 2: BUILD — Implementation & Robustness (Startup Edition)

> **Gatekeeper:** Read this file in full before creating or editing code in `./src/`, SQL scripts, or configuration files.

---

## 1. Build Principles

1. **Edit `./src/` Directly:** There are no read-only core locks in the Startup Edition (unless the repo explicitly declares one).
2. **Reuse First ("Less Code is Better"):** Reuse existing components, hooks, middlewares, and helpers before writing new ones. Follow the repository's existing naming and formatting conventions.
3. **Happy Path First + Intent Comments:** Build the happy path first. Comment the **why** (business rules, non-obvious edge cases, library quirks), not the *what*. Never delete existing helpful comments.

---

## 2. Robustness, Logging & Pre-Flight Checks

Even in a fast-moving project, silent failures waste hours. Always include:

1. **Structured Error Handling (`try-catch`):**
   * Wrap all external API calls, database queries, file I/O, and JSON/XML parsing in `try-catch`.
   * Log caught exceptions with full context and stack trace — do not swallow errors silently.
2. **Standardized Log Format** (for scripts, jobs, and backend services):
   ```text
   [YYYY-MM-DD HH:MM:SS] [SEVERITY] [Component] - Message
   ```
   * `INFO` — startup, shutdown, key milestones.
   * `WARNING` — non-fatal issues, fallback paths, or `"Query returned 0 rows to process"`.
   * `ERROR` — caught exceptions with stack traces.
3. **Pre-Flight Checks:**
   * Verify required env vars/config keys exist, input files exist and are non-empty (no 0-byte surprises), output paths are writable, and DB/API connections respond before running batch logic.
4. **Safety Check:**
   * Keep secrets in `.env` (use `<YOUR_API_KEY>` in committed templates) and ensure dev configs mute outbound email/SMS/payments (`0` / `false` / test mode).

---

## Exit Gate → Moving to `phase_finalize.md`

Before running final verification or declaring the task done, **read `phases/phase_finalize.md` in full**.
