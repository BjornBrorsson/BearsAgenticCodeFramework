# Agentic Workspace Rules & Protocols (Enterprise Edition)

Operating rules for any AI agent (Cascade, Copilot, Devin, Claude, etc.) working in this repository. Optimized for safety, traceability, and a self-improving knowledge base that makes future sessions faster.

**Core Principle:** Stability, security, and predictability take precedence over speed.
**Path Convention:** All paths are relative to the repo root (`./docs/`, `./src/`, `./tasks/`).
**Precedence (on conflict):** `Safety (§2)` → `explicit user instructions` → `this file` → `your defaults`. Everything except §2 is an overridable default; when you override one, say so and why.

---

## Core Loop (every session)

> **ORIENT → ISOLATE → GROUND → BUILD → VERIFY → RECORD**

1. **ORIENT** — read `./docs/DOCUMENTATION_INDEX.md`, the relevant `./docs/wiki/` page, `./docs/MASTER_MEMORIES.md`, and `./docs/schemas/SCHEMA_INDEX.md` before searching code. If a source is missing, create it (see the architecture blueprint's Bootstrapping SOP) rather than defaulting to repo-wide search.
2. **ISOLATE** — every task gets its own `./tasks/<task>/` folder and a spec file. Drafts, scratch scripts, and raw data stay there.
3. **GROUND** — trace `requirement → wiki/module → config → code → data` before editing. Reuse existing patterns before writing new ones. Verify schemas, API shapes, and column types; do not assume them.
4. **BUILD** — SELECT-only DB access; secrets via env/placeholders; wrap I/O in error handling and logging; follow the repo's existing conventions over the defaults in this file.
5. **VERIFY** — no fake tests; dry-run locally; cover edge cases. "Done" means verified, not written.
6. **RECORD** — update the wiki (with verified deep links), `PROJECT_LOG.md`, and `PROJECT_MEMORIES.md`; promote reusable findings to `MASTER_MEMORIES.md`. Use the routing table in §6 to decide where each finding goes.

---

## 0. Knowledge Sources (Consult First)

Read these before broad searches, edits, or script runs. If a source does not exist yet, create it rather than skipping orientation.

| Source | Read it before… | Why |
|---|---|---|
| `./docs/DOCUMENTATION_INDEX.md` | any code search | Tells you where to look; avoids repo-wide grep. |
| `./docs/wiki/<domain>.md` | touching a feature/domain | Business logic plus deep links to the implementation. |
| `./docs/MASTER_MEMORIES.md` | designing or debugging | Pre-solved framework/API/engine quirks. |
| `./docs/schemas/SCHEMA_INDEX.md` | any SQL/API/model work | Exact tables, fields, payloads; prevents wrong assumptions. |

**Repeat-Encounter Rule (Two-Strike):** Log a quirk to `MASTER_MEMORIES.md` the **second time** you hit it — or the first time if it cost more than ~30 minutes. Do not wait for a third occurrence.

---

## 1. When to Stop and Ask

Proceed by default and record assumptions in the spec/log. Stop and ask first only when:

* The request is ambiguous in a way that materially changes behavior or scope.
* An action is destructive or irreversible: production data, dropping/altering tables, deleting files you did not create, rewriting git history, force-push, sending real emails/SMS/payments.
* The choice creates expensive lock-in: a new dependency, a schema migration, or a public API/contract change.
* Continuing would require violating a §2 safety rule.

If blocked, state what you tried, the options, and your recommendation; do not stall silently.

---

## 2. Safety, Environment & Data Guardrails (non-negotiable)

Treat every environment as production-like unless told otherwise.

* **Database — SELECT only.** Never run DML/DDL (`INSERT`, `UPDATE`, `DELETE`, `MERGE`, `ALTER`, `TRUNCATE`, `DROP`) directly. Any AI-run query uses a non-locking read (e.g. `WITH (NOLOCK)` / read-uncommitted, or the engine's equivalent). Deliver change scripts as reviewable text with the note: "Review and execute this manually."
* **Read-only core.** Treat the stable core (e.g. `./src/core/` or `[CORE_LIBRARY_PATH]`) as read-only. Do not modify it unless explicitly told to.
* **No destructive file ops.** Never delete or overwrite existing files without explicit, per-case consent. Draft to new, timestamped names. Exclude `archive/`, `temp/`, build, and transient folders from copies/indexing.
* **Secrets.** Never hardcode credentials, keys, or tokens. Use env vars, secret stores, placeholders (`<YOUR_API_KEY>`), or integrated auth. Never commit secrets.
* **Mute outbound comms in non-prod.** Force mailers, SMS, push, webhooks, and live payment gateways into test mode or disabled (`0`/`false`) so test runs never reach real users.
* **Before risky restores/migrations:** verify collation/charset/version compatibility and warn on mismatch.

---

## 3. Development Workflow

### Step 1 — Isolate the task
Request three trackers: **Task Name**, **Task/Ticket ID** (e.g. `#12345`), **Component/Client**. Create `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/` and seed a spec file (`[TASK_ID]_Specs.md`) as the single source of truth. All drafting happens here.

### Step 2 — Ground before building
Search existing code, prior tasks, the Index, and the wiki for a pattern to reuse. Map `requirement → wiki/module → config → code → data`. Record decisions and assumptions in the spec.

### Step 3 — Build
Write the happy path first. Keep code compact (no needless nesting or duplication). Comment the *why* (intent, constraints, trade-offs), not the *what*. Detect and follow the repo's existing style, naming, and test conventions; the conventions in §4 are defaults for greenfield only.

### Step 4 — Make it robust
Wrap all I/O, DB, and network/API calls in error handling. Log caught exceptions through the real logger (timestamp, component, message, stack) — never bare `print`/`Write-Host` in production code.

### Step 5 — Pre-flight checks
Before main logic, validate inputs and environment: file existence/size (no 0-byte surprises), write permissions, DB/API reachability. Fail loudly, log, and exit gracefully.

---

## 4. Logging & Naming (defaults — mirror the repo if it has its own)

* **Log format:** `[YYYY-MM-DD HH:MM:SS] [SEVERITY] [Component] - Message`
  * `INFO` milestones · `WARNING` non-fatal (including the "0 rows returned" case) · `ERROR` exceptions with stack trace.
* **Artifact names:** `[PROJECT_PREFIX]_[TASK_ID]_[desc].[ext]`; scripts `..._Script_[desc].[ext]`; DB objects `..._[name]`, views `..._v_[name]`, triggers `..._tr_[name]`; suffix region/locale where relevant.

---

## 5. Testing & Verification (no fakes)

"Done" is verified, not written.

* **No placeholder tests.** Never `assert(true)`. Tests must execute real logic and cover boundaries and negatives: null/empty, oversized input, injection characters, timeouts, failure paths.
* **Dry-run locally.** Exercise the change in a safe local instance and read the logs. Treat every failure as a fix-rebuild-rerun loop before handing back to the user.
* **Transaction-safe DB tests.** When a local test must mutate data, wrap it:
  ```sql
  BEGIN TRANSACTION;
    -- exercise logic; inspect row counts / timing
  ROLLBACK TRANSACTION;  -- leave the DB unchanged
  ```

---

## 6. Self-Improving Knowledge Loop

End every session with the knowledge base more complete than it started. Route each finding to one place:

| What you learned / produced | Where it goes |
|---|---|
| What you changed this task, why, and any post-mortem | `./tasks/<task>/PROJECT_LOG.md` |
| Edge cases/quirks specific to this client/feature | `./tasks/<task>/PROJECT_MEMORIES.md` |
| How a feature/domain works + links to its code | `./docs/wiki/<domain>.md` |
| Reusable repo-wide framework/API/engine gotcha + fix | `./docs/MASTER_MEMORIES.md` |
| A new place to look / new doc | `./docs/DOCUMENTATION_INDEX.md` |
| Table / field / payload definition | `./docs/schemas/SCHEMA_INDEX.md` |

Promote local → global when a finding is (a) not task-specific, (b) likely to recur, and (c) cost real time to discover.

### Durable deep links
Raw line numbers drift and become misleading. Keep links reliable:

1. **Anchor on a stable symbol** (function/class/route name), not a bare line range. Give the line only as a hint.
2. **Date-stamp it.** Append `_Verified: YYYY-MM-DD_` so readers know its age.
3. **Verify on read.** Confirm the symbol still exists near the hint before relying on it.
4. **Repair on touch.** If you edit a linked file, update its link and verified date in the same change. A stale link is a defect.

   *Format:* `` **Charge handler** — `processCharge()` in [src/services/stripe.ts](../../src/services/stripe.ts) (~L45). _Verified: YYYY-MM-DD._ ``

---

## 7. Output & Handover

* Deliver complete, copy-paste-ready files, not fragments (except a one-line fix).
* Attach a short summary: what it does, dependencies (e.g. "Node 18+"), and run/verify/deploy steps.
* For DB scripts, restate that AI-run queries are `SELECT`-only and non-locking.
* Provide a staging/deployment manifest: changed files and DB objects in dependency order, plus rollback notes.

---

## 8. Do Not

* Skip orientation and grep the whole repo.
* Assume a column/field/endpoint exists instead of checking the schema index.
* Edit the read-only core, or run DML to test.
* Write a wiki link with a raw line number and never re-verify it.
* Put non-reusable notes in `MASTER_MEMORIES.md` (route them per §6).
* Mark a task done without a dry-run, edge cases, and updated docs.
* Invent a naming/style convention when the repo already has one.

---

## 9. Done Checklist

- [ ] Lint / compile / static checks pass.
- [ ] Local dry-run succeeded; logs reviewed.
- [ ] Edge cases exercised (null/empty/oversized/failure paths).
- [ ] Logs use correct `INFO`/`WARNING`/`ERROR` severities.
- [ ] `./docs/wiki/<domain>.md` created/updated; deep links verified and date-stamped.
- [ ] `PROJECT_LOG.md` (with post-mortem) and `PROJECT_MEMORIES.md` updated.
- [ ] Reusable findings promoted to `MASTER_MEMORIES.md` (per §6 routing).
- [ ] Staging/deploy manifest and rollback notes delivered.
