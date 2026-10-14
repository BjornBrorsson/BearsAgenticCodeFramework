# The Self-Improving Agentic Workspace — Architectural Blueprint (Enterprise Edition)

How to set up, structure, and operate a Self-Improving Agentic Workspace for an enterprise codebase. Product-agnostic and codebase-independent: drop it into any repo so AI agents (Cursor, Copilot, Devin, Windsurf, custom LLM scripts) operate safely, search efficiently, and improve the knowledge base each session.

> **Modular Progressive Disclosure Architecture:** This file explains how the workspace is built and why. Its runtime companion is the modular rulebook: **`AGENT_RULES.md`** (always-loaded core + safety guardrails + Phase/Skill Router) paired with on-demand modules in **`./phases/`** (12 phase modules) and **`./skills/`** (16 skill modules). The legacy single-file reference remains in `agentic_rules_blueprint.md`. Keep them in sync.

---

## 1. Problems It Solves

1. **Lost knowledge.** A fresh session knows nothing the last one learned, so time spent resolving an obscure quirk is lost and the next agent re-solves it.
2. **Search overhead.** Without a map, agents grep the whole repo, burning tokens and dragging irrelevant files into context.
3. **Unsafe autonomy.** Without hard guardrails, agents run destructive queries, edit core code, or leak data.

The structure is a two-loop knowledge model: a local loop per task, and a durable global loop for the whole repo.

```
        +-------------------------------------------------------------+
        |                 GLOBAL KNOWLEDGE  (Loop 2)                  |
        |  MASTER_MEMORIES.md  — reusable engine/API/framework gotchas|
        |  docs/wiki/*.md      — domain logic + durable code links    |
        |  DOCUMENTATION_INDEX / SCHEMA_INDEX — where things live      |
        +------------------------------^------------------------------+
                           promote reusable findings (per routing table)
                                       |
        +------------------------------+------------------------------+
        |                 LOCAL TASK LOOP  (Loop 1)                   |
        |  tasks/<task>/PROJECT_LOG.md       — changes + post-mortem   |
        |  tasks/<task>/PROJECT_MEMORIES.md  — task-specific quirks    |
        |  tasks/<task>/<id>_Specs.md        — source of truth         |
        +------------------------------^------------------------------+
                                       | execute
                             [ AI Agent / LLM Tooling ]
```

* **Loop 1 (local):** isolated per task. Captures what changed, why, and what was learned without polluting the repo or git.
* **Loop 2 (global):** the durable store. Reusable gotchas go in `MASTER_MEMORIES.md`; how-it-works knowledge goes in `docs/wiki/` with durable deep links into the source.

---

## 2. Directory Layout

```
[Repository Root]/
├── AGENT_RULES.md / AGENTS.md      <-- Always-loaded core rules, safety (§1) & Phase/Skill Router (§2)
├── phases/                         <-- On-demand phase modules (research, plan_and_design, investigate, teach, build, refactor, test_and_validate, finalize, debug, review, autonomous_backlog, hotfix)
├── skills/                         <-- On-demand skill modules (conventions_and_naming, kanban_workflow, subagent_orchestration, autonomous_validation_and_artifacts, database_change_scripts, etc.)
├── Tickets/                        <-- Optional Agentic Kanban board (Backlog/Ready, Ongoing, Assistance Required, Blocked, Completed)
├── docs/
│   ├── DOCUMENTATION_INDEX.md      <-- Where everything lives; search-first map
│   ├── MASTER_MEMORIES.md          <-- Reusable, repo-wide gotchas + fixes
│   ├── schemas/
│   │   └── SCHEMA_INDEX.md         <-- Tables / fields / payloads -> definitions
│   └── wiki/                       <-- One page per DOMAIN (not per file)
│       ├── system_architecture.md  <-- High-level map of the whole system
│       └── <domain>.md             <-- Domain logic + durable deep links
├── src/ (or source/)               <-- Production code; core/ is READ-ONLY for AI
├── tools/ (or scripts/)            <-- Utilities, deploy helpers, test runners
└── tasks/ (or work_in_progress/)   <-- Isolated per-task workspaces
    ├── WhatWhy.md                  <-- Explains the folder + conventions
    └── [Component] - [TaskID] - [TaskName]/
        ├── [TaskID]_Specs.md       <-- Source of truth for the task
        ├── PROJECT_LOG.md          <-- Change log + post-mortem + rollback
        ├── PROJECT_MEMORIES.md     <-- Task-specific findings
        └── Verification/           <-- Human verification guide, logs, before/after proof (+ archive/)
```

---

## 3. Knowledge Routing

Send each piece of knowledge to one home so every file stays high-signal.

| Information | Home | Lifespan |
|---|---|---|
| What changed this task, why, post-mortem | `tasks/<task>/PROJECT_LOG.md` | Task |
| Quirks specific to this client/feature | `tasks/<task>/PROJECT_MEMORIES.md` | Task |
| How a domain works + links to its code | `docs/wiki/<domain>.md` | Repo |
| Reusable framework/API/engine gotcha + fix | `docs/MASTER_MEMORIES.md` | Repo |
| A new place to look / new document | `docs/DOCUMENTATION_INDEX.md` | Repo |
| Table/field/payload definitions | `docs/schemas/SCHEMA_INDEX.md` | Repo |

Promote local → global only when the finding is (a) not task-specific, (b) likely to recur, and (c) cost real time to discover.

---

## 4. Durable Deep Links

A wiki whose links rot sends agents to the wrong line with false confidence. Raw `#L45-80` ranges drift on the next edit. Use this standard everywhere code is referenced:

1. **Anchor on a stable symbol** — a function, class, route, or export name — not a bare line range.
2. **Line number is a hint only** — prefix with `~` to signal "approximate".
3. **Date-stamp every link** with `_Verified: YYYY-MM-DD_` (optionally a git SHA/tag) so readers can gauge trust.
4. **Verify on read** — confirm the symbol still exists near the hint before relying on it.
5. **Repair on touch** — if you edit a linked file, fix its link and verified date in the same change. A stale link is a defect.

```
**Charge handler** — `processCharge()` in [src/services/stripe.ts](../../src/services/stripe.ts) (~L45). _Verified: YYYY-MM-DD._
```

> Relative-path note: from `docs/wiki/billing.md`, the repo root is `../../`, so source lives at `../../src/...`. On GitHub-hosted repos, a commit-pinned permalink is more durable still.

---

## 5. Wiki Granularity & Freshness

* **One page per domain**, not per file. Group by capability (`authentication`, `billing`, `notifications`) so a page maps a whole flow. Avoid many micro-pages.
* **Top-of-page freshness header:**
  ```
  > Status: Current | Last verified: YYYY-MM-DD | Owner-of-record: <team/role>
  ```
* **Split a page** when it exceeds roughly 200 lines or covers 3+ unrelated flows.
* A page that cannot be verified should be marked `> Status: STALE — verify before trusting` rather than trusted silently.

---

## 6. Core File Templates

### A. `docs/DOCUMENTATION_INDEX.md`
```markdown
# Documentation Index & Search Priorities
Guides agents to the right directory first; minimizes token spend and blind search.

## Directory Map
* Core logic: `./src/core/`
* Config/formats: `./src/config/`
* DB & migrations: `./src/db/`
* Integrations/APIs: `./src/api/`

## Domains (Wiki)
* Authentication & permissions: [auth](./wiki/authentication.md)
* Payments: [billing](./wiki/billing.md)
* Notifications: [notifications](./wiki/notifications.md)

## Search-First Checklist
| Goal | Look here first | Then |
|---|---|---|
| DB tables/fields | `docs/schemas/SCHEMA_INDEX.md` | `src/db/` |
| Framework/API gotcha | `docs/MASTER_MEMORIES.md` | core code |
| Business/domain logic | `docs/wiki/` | `src/core/` |
```

### B. `docs/wiki/<domain>.md`
```markdown
# Domain: <Name>
> Status: Current | Last verified: YYYY-MM-DD | Owner-of-record: <team/role>

## 1. What & Why
Business purpose and rules. Who uses it, and the key behaviors/invariants.

## 2. How It Works
The flow across controllers/services/jobs/data. A short diagram or numbered steps.

## 3. Source Map (Durable Deep Links)
* **<Role>** — `symbolName()` in [path](../../src/...) (~L10). _Verified: YYYY-MM-DD._
* **<Role>** — `ClassName` in [path](../../src/...) (~L45). _Verified: YYYY-MM-DD._

## 4. Config & Operational Keys
* `ENV_KEY` — what it controls, default, where set.

## 5. Known Gotchas
Cross-link to MASTER_MEMORIES entries; keep one-liners here.

---
## Maintenance Log
* YYYY-MM-DD (agent/author): what changed and which links were re-verified.
```

### C. `docs/MASTER_MEMORIES.md`
```markdown
# Repository Master Memories
> Purpose: reusable, repo-wide gotchas, quirks, and workarounds. Read before designing/debugging.
> Last updated: YYYY-MM-DD

## TOC
1. Framework quirks   2. DB/state anomalies   3. Third-party APIs   4. Build/CI

## Entry template (Repeat-Encounter / Two-Strike Rule)
### <index> <short title>
* **Verified:** YYYY-MM-DD (version/SHA if relevant)
* **Context:** when it happens
* **Symptom:** what you see / why it bites
* **Root cause:** the actual reason
* **Fix / Workaround:** the durable solution
* **Example:** minimal code/config snippet
* **Source:** file/PR/test where confirmed

> Log on the second encounter, or the first if it cost more than ~30 minutes.
```

### D. `tasks/WhatWhy.md`
```markdown
# The Task Workspace (`./tasks/`)
Isolates active work so the repo and git stay clean, and gives each task a permanent record.

## How to work
1. Ask for: Component/Client, Task ID, Task Name.
2. Create `./tasks/[Component] - [TaskID] - [TaskName]/`.
3. Seed `[TaskID]_Specs.md` from the request — the source of truth.
4. Draft scripts/data/templates here only.
5. Keep `PROJECT_LOG.md` and `PROJECT_MEMORIES.md` current as you go.
```

### E. `tasks/<task>/PROJECT_LOG.md`
```markdown
# Project Log — [TASK_ID]

## Change Log — YYYY-MM-DD
### 1. <File> — <one-line summary>
* **Path:** `./src/...`
* **Why:** requirement/issue it serves.
* **How:** what changed, and dependencies/migrations triggered.

## Root Cause & Prevention (Post-Mortem)
* **Root cause:** the real cause of any bug fixed this session.
* **Prevention:** the code/test/check that stops this class of bug recurring.
```

### F. `tasks/<task>/PROJECT_MEMORIES.md`
```markdown
# Project Memories — [TASK_ID]
## Component-Specific Findings
* Input format X requires leading zeros.
* API `Z` assumes GMT and rejects offsets.
```

---

## 7. Bootstrapping a Repo (first-time setup)

Run once when adopting the workspace. Generate from the actual repo — scan it, do not invent structure.

1. **Create dirs:** `docs/`, `docs/wiki/`, `docs/schemas/`, `tasks/`.
2. **Build the Index:** scan top-level folders; write `DOCUMENTATION_INDEX.md` with the real directory map and search-first table.
3. **Seed the system map:** write `wiki/system_architecture.md` describing the actual high-level components.
4. **Seed schemas:** write `schemas/SCHEMA_INDEX.md` mapping real models/tables/payloads to their definition files.
5. **Create `MASTER_MEMORIES.md`** from template C (an empty TOC is fine).
6. **Add `tasks/WhatWhy.md`** from template D.
7. **Install the rulebook:** copy `agentic_rules_blueprint.md` to `.cursorrules` / `.windsurfrules` at the root.
8. **Backfill the wiki opportunistically:** each task that touches a domain leaves behind its first or updated wiki page, so the knowledge base grows with use rather than as a separate documentation effort.

---

## 8. Runtime SOP (every task follows the Core Loop)

**A. Isolate** — gather Component/TaskID/Name → create `tasks/<task>/` → seed `<id>_Specs.md`.

**B. Orient & ground** — read the Index → relevant wiki page (follow its deep links) → MASTER_MEMORIES → SCHEMA_INDEX. Find a pattern to reuse before writing new code. Do not repo-wide grep before this.

**C. Build** — feature branch `feature/[TaskID]_[name]`; draft in the task folder; happy path first with *why* comments; follow existing conventions; wrap I/O in error handling and logging; SELECT-only DB access.

**D. Verify** — real tests (no `assert(true)`), local dry-run, edge cases; transaction-wrap any local DB mutation test.

**E. Record** — update `PROJECT_LOG.md` (with post-mortem) and `PROJECT_MEMORIES.md`; create/refresh the `docs/wiki/<domain>.md` page with verified, date-stamped deep links; promote reusable gotchas to `MASTER_MEMORIES.md` per the routing table; hand over a staging/rollback manifest.

---

## 9. Adoption Notes

* **Follow existing conventions.** If the repo already has docs/tests/naming conventions, adapt this structure to them rather than forcing the defaults.
* **Make the steps unskippable.** Put orientation and closing steps in `.cursorrules` so they run automatically, and add the Done checklist to the PR template.
* **Verify links in review.** A PR that changes linked code but not its wiki link should fail review; this keeps the documentation accurate.
* **Start small.** The full wiki is not required on day one; bootstrap the indexes and let each task grow the relevant page.

---

## 10. Drop-in Rules Stub

Place at the repo root as `.cursorrules` / `.windsurfrules` (or point the agent at the full `agentic_rules_blueprint.md`):

```markdown
# Self-Improving Workspace — Enterprise. Full rules: ./docs/agentic_rules_blueprint.md
Loop every task: ORIENT -> ISOLATE -> GROUND -> BUILD -> VERIFY -> RECORD.
1. Orient first: read docs/DOCUMENTATION_INDEX.md, the relevant docs/wiki/ page, docs/MASTER_MEMORIES.md, docs/schemas/SCHEMA_INDEX.md before searching code. If missing, create it.
2. Isolate: ask for Component, Task ID, Task Name; create ./tasks/<...>/ + a spec file; draft only there.
3. Safety (non-negotiable): SELECT-only + non-locking DB; never edit read-only core; secrets via env; mute outbound comms in non-prod; no destructive file/git ops without explicit consent.
4. Verify: no fake tests; dry-run locally; cover edge cases. Done = verified.
5. Record: update the wiki with durable, date-stamped deep links (anchor on symbol names; verify on read; repair on touch); update PROJECT_LOG/PROJECT_MEMORIES; promote reusable gotchas to MASTER_MEMORIES.
```
