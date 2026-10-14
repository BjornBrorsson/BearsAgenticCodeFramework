# Phase 2: BUILD — Implementation, Robustness & Logging (Enterprise Edition)

> **Gatekeeper:** Read this file in full **before** creating or modifying any code, SQL script, configuration, or service definition.
> **Core Safety:** All guardrails in `AGENT_RULES.md` §1 apply. Never modify read-only core modules without explicit permission; draft scripts and scratch artifacts in `./tasks/<task>/`.

---

## 1. Less Code is Better Than More Code

* **Reuse First:** Adapt proven patterns, middlewares, and utility helpers already in the repository or prior `./tasks/` solutions rather than inventing parallel abstractions. Cite what you reused.
* **Match Repo Conventions:** Always detect and follow the repository's existing code style, directory layout, naming, and error-handling conventions. The conventions in `../skills/conventions_and_naming.md` serve as greenfield defaults when the repo has none.
* **Compact & Readable:** Collapse duplicate conditional branches, use guard clauses to avoid deep nesting, and keep functions focused.

---

## 2. Step-by-Step Build Protocol

### Step 3 — Build the "Happy Path" First
* Implement the primary happy path first and verify its end-to-end data flow before adding secondary edge branches.
* **Comment the *Why*, Not the *What*:** Document business invariants, protocol constraints, concurrency/locking assumptions, and why non-obvious design choices were made. Never strip existing meaningful comments.

### Step 4 — Make It Robust (Structured Error Handling)
* **Wrap All Volatile Operations:** Every file I/O operation, database query, external HTTP/RPC call, and serialization boundary **must** be wrapped in structured `try-catch` / error-handling blocks.
* **Formal Logger Only:** Write all caught exceptions through the application's structured logger (with timestamp, severity, component name, message, and full stack trace) — **never** rely on bare `print`, `console.log`, or `Write-Host` in production-bound code.

### Step 5 — Standardized Logging Format

Unless the repository enforces its own structured JSON/ telemetry schema, log startup, shutdown, major milestones, row counts, and exceptions in this format:

```text
[YYYY-MM-DD HH:MM:SS] [SEVERITY] [ComponentName] - Message
```

* **INFO:** Process startup/shutdown, configuration summary, and major milestones (with counts/duration).
* **WARNING:** Non-fatal issues, fallback paths, or empty datasets (**including `"Query returned 0 rows to process"`**).
* **ERROR:** Caught exceptions, failed pre-flight checks, or contract violations (**always include stack trace**).

### Step 6 — Pre-Flight Checks (Mandatory Before Main Logic)

Validate all prerequisites before executing main business logic:
1. **Input & File Validation:** Confirm required files/paths exist (`Test-Path` / `fs.existsSync`) and are non-empty (guard against 0-byte input files).
2. **Permissions:** Verify write permissions on output and log directories before starting work.
3. **Connectivity & Config:** Verify database/API reachability and required environment/config keys; fail fast with an `[ERROR]` log entry and clean exit if missing.
4. **Non-Prod Comms Guard:** Confirm outbound mailers, SMS, webhooks, and live payment gateways are muted (`0` / `false` / sandbox) in non-prod configs.

---

## 3. Domain Skill Dispatch During Build

* **Naming artifacts & test structure:** Load `../skills/conventions_and_naming.md`.
* **Writing SQL migrations or data-fix scripts:** Load `../skills/database_change_scripts.md` (deliver scripts for human review; never run DML/DDL on shared/prod DBs).
* **Secrets & Compliance:** Load `../skills/security_privacy_and_compliance.md` (use env vars or `<YOUR_API_KEY>` placeholders).

---

## Exit Gate → Moving to `phase_finalize.md`

Before running final verification, archiving `./Verification/` evidence, updating docs, or declaring the task done, **read `phases/phase_finalize.md` in full**.
