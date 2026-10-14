# Skill: Project Kickoff & Templates (Startup / Solo Edition)

> **Load When:** Bootstrapping `docs/`, `tasks/`, and `Tickets/` into a new side project or creating a new feature folder under `./tasks/<feature>/`.
> **Architecture Reference:** `../workspace_architecture_blueprint_SE.md`.

---

## 1. Fast Bootstrap Sequence (New Repo / Side Project)

1. Create directories: `./docs/wiki/`, `./tasks/`, and `./Tickets/` (see `kanban_workflow.md`).
2. Create `./docs/DOCUMENTATION_INDEX.md` and `./docs/MASTER_MEMORIES.md` using the templates below.
3. Add `./docs/schemas/SCHEMA_INDEX.md` once the project has a database or external API worth indexing.
4. Grow `./docs/wiki/<feature>.md` lazily — write or update a page whenever you touch a feature.

---

## 2. Copy-Paste Templates

### A. `./docs/DOCUMENTATION_INDEX.md`
```markdown
# Documentation Index
Open the right folder directly instead of grepping the whole repo.

## Directory Map
- Source code: `./src/`
- Database / models: `./src/db/`
- Task scratchpads: `./tasks/`
- Kanban board: `./Tickets/`

## Feature Wiki
- Overview: [system_overview](./wiki/system_overview.md)
```

### B. `./docs/wiki/<feature>.md`
```markdown
# Feature: <Name>
> Status: Current | Last verified: YYYY-MM-DD

## What It Does
2–3 sentences explaining the behavior and key invariants.

## Source Map (Durable Deep Links)
- **<Role>** — `symbolName()` in [src/...](../../src/...) (~L20). _Verified: YYYY-MM-DD._

## Config & Env Keys
- `ENV_KEY` — purpose and default (set in `.env`).

## Gotchas
- One-liners; link to `../MASTER_MEMORIES.md` for deeper quirks.
```

### C. `./docs/MASTER_MEMORIES.md`
```markdown
# Master Memories
> Reusable bugs, framework quirks, and workarounds (Two-Strike or >30-min rule).

### <Short Title>
- **Verified:** YYYY-MM-DD
- **Symptom:** What goes wrong
- **Root Cause:** Why it happens
- **Fix:** The durable workaround + snippet
```

### D. `./tasks/<feature>/PROJECT_LOG.md` & `PROJECT_MEMORIES.md`
```markdown
# Log: <feature>
## Changes — YYYY-MM-DD
- `<file>` — what changed and why.
- **Verification:** Saved in `./Verification/`.

## Post-Mortem & Rollback
- **Cause & Prevention (if bugfix):** <1–2 lines>
- **Rollback:** <How to revert>
```
