# Skill: Agentic Kanban Workflow (Startup Edition — Default Project Tracker)

> **Load When:** A project has >3 tasks, spans multiple sessions, or has a `Tickets/` folder. For solo developers and small teams, **Agentic Kanban is the recommended default task tracker** — zero cloud lock-in, 100% native Git + Markdown.
> **Extension:** Compatible with the **Agentic Kanban** VS Code / Windsurf / Devin / Cursor extension (`agentic-kanban-*.vsix`, source: `C:\Personal Code Projects\Kanban Extension`).

---

## 1. Board Structure (`Tickets/`)

```text
Tickets/
├── 00_Plan.md                             <-- Roadmap, epics & architecture vision
├── config.md                              <-- Humans, CLI/IDE agents & multi-model routing
├── Backlog/
│   ├── Whatwhy.md                         <-- Column guidance (renders as UI tooltip `(i)`)
│   ├── Needs further specification/       <-- Draft ideas needing refinement
│   └── Ready/                             <-- Specified cards ready to build
├── Ongoing/                               <-- Active work in progress
├── Assistance Required/                   <-- Needs human decision, API key, or review
├── Blocked/                               <-- Waiting on another ticket or external issue
└── Completed/                             <-- Verified, finished cards (`[x]`)
```

### One-Line Bootstrap (Bash)
```bash
mkdir -p Tickets/"Backlog/Needs further specification" Tickets/"Backlog/Ready" Tickets/Ongoing Tickets/"Assistance Required" Tickets/Blocked Tickets/Completed
```

---

## 2. Ticket Template (`Tickets/<Column>/<ID>_<slug>.md`)

```markdown
# <ID> — <Title>

| Field | Value |
|-------|-------|
| **Epic** | <Epic / Feature Area> |
| **Type** | Feature \| Bug \| Architecture \| Documentation |
| **Priority** | Low \| Normal \| High \| Critical |
| **Status** | Backlog \| Ongoing \| Assistance Required \| Blocked \| Completed |
| **Assignee** | <Agent ID or Human ID from config.md> |
| **Estimate** | S \| M \| L |
| **Depends on** | <Ticket ID or '—'> |
| **Labels** | `frontend`, `api`, `db` |

## Summary
Concise description of what we are building and why.

## Requirements & Scope
Files touched, data model/schema notes, and edge cases.

## Acceptance Criteria
- [ ] Requirements specified
- [ ] Implemented (`phase_build.md`)
- [ ] Verified locally & proof saved (`phase_finalize.md`)

## Work Log
- **YYYY-MM-DD**: Claimed ticket and started implementation.
```

---

## 3. Agent Lifecycle (7 Steps)

1. **Discover:** Pick from `Tickets/Backlog/Ready/` (or `Backlog/`). Verify all tickets in `| **Depends on** |` are already in `Tickets/Completed/`.
2. **Claim:** `git mv "Tickets/Backlog/Ready/<card>.md" "Tickets/Ongoing/<card>.md"`, set `Status: Ongoing` and `Assignee`, and add a dated `## Work Log` line.
3. **Specify (if draft):** Flesh out `## Summary`, scope, and `- [ ]` checkboxes before coding.
4. **Build:** Implement within bounded scope (`phases/phase_build.md`).
5. **Verify:** Run build, tests, and local dry-run (`phases/phase_finalize.md`). Tick `- [x]` as each criterion passes.
6. **Block / Ask:** Need human input or credentials → `git mv` to `Tickets/Assistance Required/` and log the question. Waiting on dependency → `git mv` to `Tickets/Blocked/`.
7. **Complete:** `git mv "Tickets/Ongoing/<card>.md" "Tickets/Completed/<card>.md"`, set `Status: Completed`, check all `- [x]`, append final `## Work Log` entry, and update `Tickets/00_Plan.md` if a milestone finished.

---

## 4. Multi-Model Routing (`Tickets/config.md`) & Invariants

* **Model Tiers:** Route `discovery`/`quick-fix` to `fast-discovery` (e.g. local Ollama `gemma4:e4b`), `implementation`/`verification`/`refactor` to `standard-coder`, and `architecture`/`escalation` to `deep-reasoner`.
* **Invariants:** Work logs are **append-only** · Always use `- [ ]` / `- [x]` checkboxes · Always use `git mv` to move cards · Keep `Status` matched to folder · Never delete `Whatwhy.md` or `00_Plan.md`.
