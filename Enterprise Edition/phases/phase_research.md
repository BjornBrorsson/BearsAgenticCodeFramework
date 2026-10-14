# Phase 1: RESEARCH — ORIENT → ISOLATE → GROUND (Enterprise Edition)

> **Gatekeeper:** Read this file in full at the start of **every** task — before searching code, requesting task metadata, creating `./tasks/<task>/`, or designing changes.
> **Core Safety:** All guardrails in `AGENT_RULES.md` §1 apply unconditionally.

---

## 1. ORIENT — Consult Curated Knowledge Sources First

Before running broad, expensive searches across the repository, **always** check these four curated indexes first. If any of them does not exist yet in the repository, bootstrap it per `../skills/project_kickoff.md` rather than defaulting to blind grep.

| Priority | Source | Location | Purpose |
|---|---|---|---|
| **1** | **Documentation Index** | `./docs/DOCUMENTATION_INDEX.md` | Master directory map and "Search-First Checklist" telling you which folder owns each concern. |
| **2** | **Domain Wiki** | `./docs/wiki/<domain>.md` | Business logic, end-to-end architecture flows, config keys, and **Durable Deep Links** to source symbols. |
| **3** | **Master Memories** | `./docs/MASTER_MEMORIES.md` | Pre-solved framework, engine, database, and third-party API quirks and workarounds. |
| **4** | **Schema Index** | `./docs/schemas/SCHEMA_INDEX.md` | Authoritative mapping of database tables, views, columns, and API payloads to their schema definitions. |

### 1.1 Repeat-Encounter Rule (Two-Strike)

Log a framework, engine, or API quirk to `./docs/MASTER_MEMORIES.md` the **second** time you encounter it — or the **first** time if it cost more than ~30 minutes to diagnose. Do not let hard-won operational knowledge evaporate.

### 1.2 Durable Deep Links Standard

Raw line numbers (`#L45-80`) drift on edit. Whenever referencing code in wiki pages, specs, memories, or logs:
1. **Anchor on a stable symbol** (class name, function/method name, route handler, SQL object), not a bare line range.
2. **Line numbers are hints only** — prefix with `~` (e.g. `~L45`).
3. **Date-stamp every link** with `_Verified: YYYY-MM-DD._`
4. **Verify on read & repair on touch** — if you edit a linked file, update its link and `_Verified:` date in the same change.
* *Example:* `` **Charge Handler** — `processCharge()` in [src/services/stripe.ts](../../src/services/stripe.ts) (~L45). _Verified: 2026-10-13._ ``

---

## 2. ISOLATE — Request Trackers & Create the Task Workspace

Every task must be isolated in `./tasks/` so drafting, scratch scripts, and verification outputs never pollute the main source tree.

### 2.1 Request Three Mandatory Trackers

Ensure you have:
1. **Component / Client Name** (e.g. `Billing`, `Auth`, `ClientA`)
2. **Task / Ticket ID** (e.g. `#12345`, `JIRA-402`)
3. **Task Name / Short Slug** (e.g. `InvoiceRetryQueue`)

### 2.2 Create the Isolated Task Folder

Create `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/` containing:
* `[TASK_ID]_Specs.md` — **Single Source of Truth** for requirements, architecture mapping, schema verification, and deliverables.
* `PROJECT_LOG.md` — Chronological change log, post-mortem, and rollback plan.
* `PROJECT_MEMORIES.md` — Component- or client-specific quirks discovered during the task.
* `Verification/` and `Verification/archive/` — Human-reviewable test outputs and logs.
*(See `../skills/project_kickoff.md` for copy-paste templates.)*

### 2.3 Larger / Multi-Session Projects → Use Agentic Kanban

When a project involves **more than 3 distinct subtasks**, spans multiple sessions/agents, or decomposes an epic:
* Load `../skills/kanban_workflow.md`.
* Use or scaffold a `Tickets/` board (`00_Plan.md`, `config.md`, `Backlog/Ready/`, `Backlog/Needs further specification/`, `Ongoing/`, `Assistance Required/`, `Blocked/`, `Completed/`).
* Keep `[TASK_ID]_Specs.md` as the master specification while tracking execution subtasks as Markdown cards moved via `git mv`.

---

## 3. GROUND — Map Requirement to Architecture Before Editing

Complete this grounding trace inside `[TASK_ID]_Specs.md` before entering `phase_build.md`:

```text
Requirement → Wiki / Domain Module → Surface (UI / Job / Service / API / Report) → Config → Code Symbols → DB Tables & Columns
```

### Grounding Checklist

- [ ] **Reuse Check ("Less Code is Better"):** Searched `./src/`, prior `./tasks/`, `./docs/wiki/`, and `./docs/MASTER_MEMORIES.md` for an existing pattern to reuse. **Cited exact source paths** in `[TASK_ID]_Specs.md`.
- [ ] **Schema Verification:** Every table, column, and API payload touched has been verified in `./docs/schemas/SCHEMA_INDEX.md` and the underlying schema/model file (exact name, datatype, nullability, indexes).
- [ ] **Conventions Check:** Loaded `../skills/conventions_and_naming.md` and confirmed repo naming/style conventions.
- [ ] **Stop-Gate Check (§1.1):** Confirmed no unclarified ambiguities exist that affect DB state, financial logic, or public contracts. If any exist, **STOP and ask the user now**.
