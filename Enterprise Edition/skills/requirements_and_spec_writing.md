# Skill: Requirements Extraction & Specification Authoring (Enterprise Edition)

> **Load When:** Translating raw tickets, emails, chat logs, or PRDs into `[TASK_ID]_Specs.md` or decomposing an epic into **Agentic Kanban** cards (`Tickets/Backlog/`).

---

## 4-Step Spec Extraction Protocol

1. **Extract Explicit Facts:** Component/Client, Ticket ID, target environments, and requested deliverables.
2. **Ground Implicit Constraints:** Check `./docs/wiki/<domain>.md`, `./docs/schemas/SCHEMA_INDEX.md`, and `./docs/MASTER_MEMORIES.md` for existing domain invariants and naming conventions.
3. **Isolate Open Questions:** Never silently guess business or financial rules. Put unresolved ambiguities in an `## Open Questions` table and ask the user before coding.
4. **Write Binary, Testable Acceptance Criteria (`- [ ]`):** Every criterion must state **Given / When / Then** including expected `[INFO]`/`[WARNING]`/`[ERROR]` log behavior and DB state.
5. **Decompose if >3 Subtasks:** Place ready subtasks in `Tickets/Backlog/Ready/` and drafts needing answers in `Tickets/Backlog/Needs further specification/` (see `kanban_workflow.md`).
