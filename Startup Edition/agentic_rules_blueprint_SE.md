# Agentic Workspace Rules & Protocols (Startup Edition)

Operating rules for any AI agent (Cascade, Copilot, Devin, Claude, etc.) working in this repository. Tuned for speed and low overhead, while keeping a **self-improving knowledge base** so no problem is solved twice.

This edition is deliberately lightweight: agents may edit core files directly, no ticket IDs or filename prefixes are required, and process is minimal. The substantive difference from the Enterprise edition is *policy strictness*, not tone.

**Core Principle:** Ship quickly without breaking production or losing knowledge.
**Path Convention:** Paths are relative to the repo root (`./docs/`, `./src/`, `./tasks/`).
**Precedence (on conflict):** `Safety (§1)` → `user instructions` → `this file` → `your defaults`. Anything but §1 is an overridable default; if you override one, say so.

---

## Core Loop (every session)

> **ORIENT → ISOLATE → BUILD → VERIFY → RECORD**

1. **ORIENT** — read `./docs/DOCUMENTATION_INDEX.md`, the relevant `./docs/wiki/` page, and `./docs/MASTER_MEMORIES.md` before searching code. If a source is missing, create it.
2. **ISOLATE** — keep scratch work in `./tasks/<feature>/` so the repo and git stay clean.
3. **BUILD** — edit `./src/` directly. Reuse existing patterns. Comment the *why*; match the repo's existing style.
4. **VERIFY** — run it. No fake tests. Clear build/lint errors before "done".
5. **RECORD** — update the wiki (with durable links), the project log, and register any quirk you hit twice.

---

## 0. Consult First

Read these before broad searches, edits, or debugging. If a source does not exist yet, create it instead of skipping orientation.

| Source | Read it before… | Why |
|---|---|---|
| `./docs/DOCUMENTATION_INDEX.md` | searching the codebase | Tells you which folder to open; avoids repo-wide grep. |
| `./docs/wiki/<feature>.md` | refactoring a feature | Feature logic plus **deep links** to the code. |
| `./docs/MASTER_MEMORIES.md` | debugging or adding an integration | Known quirks are already solved here. |

**Two-Strike Rule:** Log a quirk to `MASTER_MEMORIES.md` the **second time** you hit it — or the first time if it cost more than ~30 minutes.

---

## 1. Safety (hard rules)

* **No hardcoded secrets.** Keys, tokens, and passwords go in `.env` or environment variables — never in code or git.
* **Mute outbound comms in dev.** Set email, SMS, push, and live payment APIs to test mode or disabled so test runs never reach real users.
* **Check the schema before SQL.** Verify real table/column names in `./docs/schemas/SCHEMA_INDEX.md` or the models. Never run destructive SQL against a shared or production database.
* **Stay reversible.** Commit or stash before large refactors so changes can be rolled back.

---

## 2. Proceed vs. Ask

Proceed by default and note assumptions in the log. Ask first only for: irreversible actions (production data, unrecoverable deletes, history rewrites, real payments/messages) or high lock-in choices (new core dependency, schema migration, public API change). When blocked, state the options and your recommendation rather than stalling.

---

## 3. Build

1. **Edit core directly** in `./src/`; there are no read-only locks in this edition.
2. **Reuse first.** Check the wiki/index for an existing pattern before writing new code.
3. **Compact code, comments on intent.** Explain *why*, not *what*. Follow the repo's existing conventions rather than introducing new ones.
4. **Guard volatile operations.** Wrap external API calls, file I/O, and DB access in error handling with a log line.

---

## 4. Verification

* **No fake tests.** Any test must exercise real logic — never `assert(true)`. Cover obvious nulls/empties and the failure path.
* **Run it.** Exercise the endpoint/script/flow and read the logs. Clear all build and lint errors before declaring done.
* **Safe DB tests.** For local DB tests, wrap mutations in `BEGIN TRANSACTION … ROLLBACK TRANSACTION` to check counts and performance without persisting changes.

---

## 5. Knowledge Base

Route each finding to one home; do not put everything in one file.

| What you learned | Where it goes |
|---|---|
| What you changed + any bug fixed | `./tasks/<feature>/PROJECT_LOG.md` |
| A quirk specific to this feature | `./tasks/<feature>/PROJECT_MEMORIES.md` |
| How a feature works + links to its code | `./docs/wiki/<feature>.md` |
| A reusable bug/quirk any feature could hit | `./docs/MASTER_MEMORIES.md` |
| A new doc/place worth knowing | `./docs/DOCUMENTATION_INDEX.md` |

Promote a finding from local to global when any future feature could hit it.

### Durable deep links
Raw line numbers drift on edit. Keep links reliable:
* **Anchor on the symbol** (function/class/route name), not a bare line range.
* **Line is a hint only:** write `~L42`, and append `_Verified: YYYY-MM-DD_`.
* **Verify on read:** confirm the symbol still exists near the hint before relying on it.
* **Repair on touch:** if you edit a linked file, update its link and verified date in the same change.
  ```
  **Webhook handler** — `handleStripeWebhook()` in [src/routes/billing.ts](../../src/routes/billing.ts) (~L10). _Verified: YYYY-MM-DD._
  ```

---

## 6. Do Not

* Grep the whole repo before checking the index/wiki.
* Guess a column or endpoint instead of checking the schema/models.
* Leave a wiki link with a raw line number you never re-verify.
* Put non-reusable notes in `MASTER_MEMORIES.md` (route them per §5).
* Mark a task done without running it.

---

## 7. Done Checklist

- [ ] Builds with no compile/lint errors.
- [ ] Ran it; happy path and obvious edge cases work.
- [ ] `./docs/wiki/<feature>.md` created/updated; deep links verified and date-stamped.
- [ ] `PROJECT_LOG.md` updated (with a one-line post-mortem if a bug was fixed).
- [ ] Any twice-encountered quirk logged in `MASTER_MEMORIES.md`.
- [ ] Short note to the user: files changed and how to run it.
