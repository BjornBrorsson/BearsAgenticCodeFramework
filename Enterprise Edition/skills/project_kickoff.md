# Skill: Project Kickoff & Standard Templates (Enterprise Edition)

> **Load When:** Bootstrapping the Self-Improving Agentic Workspace into a repository or creating a new task folder under `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/`.
> **Architecture Reference:** `../workspace_architecture_blueprint.md`.

---

## 1. Repository Bootstrap Sequence (First-Time Setup)

If `./docs/DOCUMENTATION_INDEX.md`, `./docs/MASTER_MEMORIES.md`, `./docs/schemas/SCHEMA_INDEX.md`, or `./tasks/` do not exist yet:
1. Create directories: `./docs/wiki/`, `./docs/schemas/`, `./tasks/`, and (for multi-ticket work) `./Tickets/`.
2. Scan the actual repository structure and create `./docs/DOCUMENTATION_INDEX.md`, `./docs/MASTER_MEMORIES.md`, and `./docs/schemas/SCHEMA_INDEX.md` using the templates below.
3. Seed `./docs/wiki/` pages lazily as domains are touched — one page per **domain** (not per file).

---

## 2. Task Kickoff Sequence (Every Task)

1. Collect the 3 trackers: **Component/Client**, **Task/Ticket ID**, **Task Name**.
2. Create `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/` and subdirectories `Verification/` and `Verification/archive/`.
3. Seed `[TASK_ID]_Specs.md`, `PROJECT_LOG.md`, and `PROJECT_MEMORIES.md` from the templates below.
4. If the task has >3 subtasks or spans multiple sessions, also scaffold `Tickets/` per `kanban_workflow.md`.

---

## 3. Copy-Paste Templates

### A. `./tasks/<task>/[TASK_ID]_Specs.md` (Single Source of Truth)

```markdown
# Specification: [COMPONENT] — [TASK_ID] ([TASK_NAME])

| Field | Value |
|---|---|
| **Component / Client** | [COMPONENT] |
| **Ticket ID** | `[TASK_ID]` |
| **Status** | Draft / In Progress / Verified / Staged |
| **Last Updated** | YYYY-MM-DD |

## 1. Requirement & Scope
- **Objective:** <Business problem and expected outcome>
- **In Scope:** <Exact deliverables>
- **Assumptions & Out of Scope:** <Documented assumptions>

## 2. Grounding & Architecture Map
- **Domain Wiki Page:** `./docs/wiki/<domain>.md`
- **Reused Patterns & Citations:** <Paths & symbols with `_Verified: YYYY-MM-DD._`>
- **Files / Services Touched:** <List>
- **Database Tables & Columns (Verified in `SCHEMA_INDEX.md`):** <List with exact types/nullability>
- **Config Keys / Flags:** <List>

## 3. Edge Cases & Robustness
- **0 Rows / Empty Input:** <Logs `[WARNING]` and exits cleanly>
- **Invalid Input / Failure Path:** <Pre-flight + `try-catch` + `[ERROR]` log>
- **Rollback Strategy:** <How to revert cleanly>

## 4. Deliverables Checklist
- [ ] Implementation complete (`phase_build.md`)
- [ ] Automated tests & local dry-run verified (`phase_finalize.md`)
- [ ] Verification artifacts saved in `./Verification/` (prior runs in `./Verification/archive/`)
- [ ] Wiki, `PROJECT_LOG.md`, and `PROJECT_MEMORIES.md` updated
```

### B. `./tasks/<task>/PROJECT_LOG.md`

```markdown
# Project Log — [COMPONENT] — [TASK_ID]

## Chronological Change Log — YYYY-MM-DD
### 1. `<File Path>` — <One-line summary>
- **Why:** <Requirement or defect addressed>
- **How:** <Key logic, invariants, and dependencies>
- **Verification:** <Saved in `./Verification/...`>

---

## Post-Mortem & Rollback Plan
- **Root Cause (if bugfix):** <Exact technical root cause>
- **Prevention:** <Test or guardrail added to prevent recurrence>
- **Rollback Steps:**
  1. <Code/config revert step>
  2. <Companion SQL rollback step>
```

### C. `./tasks/<task>/PROJECT_MEMORIES.md`

```markdown
# Project Memories — [COMPONENT] — [TASK_ID]

> **Scope:** Component- or client-specific quirks discovered during this task. Promote reusable repo-wide gotchas (Two-Strike or >30 min) to `./docs/MASTER_MEMORIES.md`.

## Discovered Constraints & Quirks
- **[YYYY-MM-DD] <Topic>:** <Details + Durable Deep Link `_Verified: YYYY-MM-DD._`>
```
