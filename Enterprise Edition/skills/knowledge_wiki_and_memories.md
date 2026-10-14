# Skill: Knowledge Wiki, Master Memories & Durable Deep Links (Enterprise Edition)

> **Load When:** Consulting or updating `./docs/wiki/`, `./docs/MASTER_MEMORIES.md`, `./docs/DOCUMENTATION_INDEX.md`, or `./docs/schemas/SCHEMA_INDEX.md`, or writing Durable Deep Links.

---

## 1. Two-Loop Knowledge Architecture

| Loop | File / Location | What Belongs Here | Lifespan |
|---|---|---|---|
| **Loop 1 (Local Task)** | `./tasks/<task>/[TASK_ID]_Specs.md` | Task requirements, grounding map, design decisions | Task |
| **Loop 1 (Local Task)** | `./tasks/<task>/PROJECT_LOG.md` | Chronological changes, verification summary, post-mortem, rollback plan | Task |
| **Loop 1 (Local Task)** | `./tasks/<task>/PROJECT_MEMORIES.md` | Quirks or constraints specific to this single client/feature | Task |
| **Loop 2 (Global Repo)** | `./docs/wiki/<domain>.md` | How a domain works end-to-end + Durable Deep Links to source code | Repo-wide |
| **Loop 2 (Global Repo)** | `./docs/MASTER_MEMORIES.md` | Reusable framework, DB, engine, or API gotchas + fixes (Two-Strike or >30 min) | Repo-wide |
| **Loop 2 (Global Repo)** | `./docs/DOCUMENTATION_INDEX.md` | High-level folder map and Search-First checklist | Repo-wide |
| **Loop 2 (Global Repo)** | `./docs/schemas/SCHEMA_INDEX.md` | Table, view, column, and API payload index | Repo-wide |

**Promotion Rule:** Promote a finding from Loop 1 → Loop 2 when it is (a) not task-specific, (b) likely to recur, and (c) cost real time to discover. **Always anonymize** client/tenant names, IDs, and ticket numbers before promoting to Loop 2.

---

## 2. Durable Deep Links Specification

Raw line numbers (`#L45-80`) drift whenever files change. Every code reference in wiki pages, memories, and specs must follow these 5 rules:

1. **Anchor on a Stable Symbol:** Name the class, function, method, route handler, or SQL object — never a bare line range.
2. **Line Number is a Hint Only:** Prefix with `~` (e.g. `~L45`).
3. **Date-Stamp Every Link:** Append `_Verified: YYYY-MM-DD._`
4. **Verify on Read:** Confirm the symbol still exists near the hint before relying on it.
5. **Repair on Touch:** If you edit a linked file, update its link and `_Verified:` date in the same change. A stale link is a defect.

* **Format Example:**
  `` **Charge Handler** — `processCharge()` in [src/services/stripe.ts](../../src/services/stripe.ts) (~L45). _Verified: 2026-10-13._ ``

---

## 3. Wiki Granularity & Freshness Standard

* **One Page Per Domain (Not Per File):** Group by business capability (`authentication.md`, `billing.md`, `notifications.md`). Split a page when it exceeds ~200 lines or covers 3+ unrelated flows.
* **Freshness Header at Top of Every Wiki Page:**
  ```markdown
  > Status: Current | Last verified: YYYY-MM-DD | Owner-of-record: <team/role>
  ```
* **`MASTER_MEMORIES.md` Entry Template (Two-Strike Rule):**
  ```markdown
  ### <Index> <Short Descriptive Title>
  * **Verified:** YYYY-MM-DD
  * **Context:** When this happens
  * **Symptom:** What error or behavior you observe
  * **Root Cause:** Why it happens under the hood
  * **Fix / Workaround:** The durable solution + minimal code snippet
  * **Source:** File/symbol (`~L45`) where confirmed
  ```
