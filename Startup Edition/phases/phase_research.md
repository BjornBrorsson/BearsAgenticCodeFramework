# Phase 1: RESEARCH — ORIENT → ISOLATE → GROUND (Startup Edition)

> **Gatekeeper:** Read this file in full at the start of every task — before searching code or editing `./src/`.

---

## 1. ORIENT — Consult Curated Sources First

Before running repo-wide searches, check these sources first. If any is missing, create it per `../skills/project_kickoff.md` rather than skipping orientation.

| Priority | Source | Location | Why |
|---|---|---|---|
| **1** | **Documentation Index** | `./docs/DOCUMENTATION_INDEX.md` | Tells you which folder owns each feature; avoids blind grep. |
| **2** | **Feature Wiki** | `./docs/wiki/<feature>.md` | How the feature works + **Durable Deep Links** to source symbols. |
| **3** | **Master Memories** | `./docs/MASTER_MEMORIES.md` | Known bugs, framework quirks, and integration gotchas already solved. |
| **4** | **Schema Index / Models** | `./docs/schemas/SCHEMA_INDEX.md` (or ORM models) | Exact table, column, and payload names before writing SQL/API code. |

* **Two-Strike Rule:** Log any quirk to `./docs/MASTER_MEMORIES.md` the **second** time you hit it — or the **first** time if it cost >30 minutes.
* **Durable Deep Links:** Anchor on the symbol name (`handleStripeWebhook()`), prefix line hints with `~` (`~L42`), append `_Verified: YYYY-MM-DD._`, and repair links whenever you touch the file.

---

## 2. ISOLATE — Set Up Scratch Space or Claim a Kanban Ticket

* **Single Task / Feature:** Create `./tasks/<feature-slug>/` (plus `Verification/` and `Verification/archive/`) to hold scratch scripts, notes, `PROJECT_LOG.md`, and `PROJECT_MEMORIES.md` so the repo root stays clean. No formal ticket ID is required.
* **Multi-Session / Larger Project (>3 Tasks):** Load `../skills/kanban_workflow.md` and use the filesystem-native `Tickets/` board (`Backlog/Ready/`, `Ongoing/`, `Assistance Required/`, `Blocked/`, `Completed/`). Claim your card by moving it to `Tickets/Ongoing/` via `git mv`.

---

## 3. GROUND — Map Before Editing

- [ ] Checked `./docs/wiki/` and `./src/` for existing helpers/patterns to reuse ("less code is better").
- [ ] Verified real table/column/endpoint shapes in `SCHEMA_INDEX.md` or models.
- [ ] Confirmed no destructive or high-lock-in ambiguity exists (if it does, ask the user before building).
