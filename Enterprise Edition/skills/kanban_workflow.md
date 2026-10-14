# Skill: Agentic Kanban Workflow (Enterprise Edition — Multi-Ticket & Multi-Agent Projects)

> **Load When:** Work spans multiple sessions or agents, an epic needs decomposing into >3 work items, or a `Tickets/` board exists in the repository.
> **Tooling:** Compatible with the **Agentic Kanban** VS Code / Windsurf / Devin / Cursor extension (`agentic-kanban-*.vsix`, source: `C:\Personal Code Projects\Kanban Extension`).

---

## 1. Core Philosophy: Filesystem as Single Source of Truth

* **100% Native Filesystem & Git:** No external database or proprietary API required. Folders represent columns, `.md` files represent tickets, and `git mv` transitions state.
* **Real-Time UI Sync:** Moving or editing a Markdown ticket on disk immediately updates the human's glassmorphic Kanban board in VS Code via `FileSystemWatcher`.
* **Enterprise Integration:** Place `Tickets/` at the repository root (for repo-wide roadmaps) or inside `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/Tickets/` (for epic-scoped boards). Multiple boards in a workspace are auto-discovered and aggregated.

---

## 2. Board Directory Structure

```text
Tickets/                                   <-- Board Root
├── 00_Plan.md                             <-- Master roadmap, epics, architecture vision
├── config.md                              <-- Assignees (humans + CLI/IDE agents) & multi-model routing
├── .templates/                            <-- Reusable ticket templates (feature.md, bug.md, task.md)
│
├── Backlog/                               <-- Column 1: Planned, unclaimed work
│   ├── Whatwhy.md                         <-- Column guidance (renders as UI header tooltip `(i)`)
│   ├── Needs further specification/       <-- Sub-status filter chip: drafts needing refinement
│   │   └── Whatwhy.md
│   └── Ready/                             <-- Sub-status filter chip: fully specified, ready for pickup
│       └── Whatwhy.md
│
├── Ongoing/                               <-- Column 2: Active tasks currently in progress
│   └── Whatwhy.md
├── Assistance Required/                   <-- Column 3: Waiting on human review, credentials, or decisions
│   └── Whatwhy.md
├── Blocked/                               <-- Column 4: Halted by unfinished dependencies or external issues
│   └── Whatwhy.md
└── Completed/                             <-- Column 5: Verified, finished tickets with all `[x]` checked
    └── Whatwhy.md
```

### Quick Bootstrap Snippet (Bash / PowerShell)

```bash
mkdir -p Tickets/"Backlog/Needs further specification" Tickets/"Backlog/Ready" Tickets/Ongoing Tickets/"Assistance Required" Tickets/Blocked Tickets/Completed
for d in Tickets/Backlog Tickets/"Backlog/Needs further specification" Tickets/"Backlog/Ready" Tickets/Ongoing Tickets/"Assistance Required" Tickets/Blocked Tickets/Completed; do
  [ -f "$d/Whatwhy.md" ] || printf "# %s\nColumn guidance for %s.\n" "$(basename "$d")" "$d" > "$d/Whatwhy.md"
done
```

---

## 3. Standard Ticket Markdown Schema

File naming: `<EPIC>-<NNN>_<short-slug>.md` (e.g. `PAY-001_retry-queue-schema.md`) or `ticket-YYYY-MM-DD-<slug>.md`.

```markdown
# <Ticket ID> — <Ticket Title>

| Field | Value |
|-------|-------|
| **Epic** | <Epic Name or ID> |
| **Type** | Feature \| Bug \| Architecture \| Documentation |
| **Priority** | Low \| Normal \| High \| Critical |
| **Status** | Backlog \| Ongoing \| Assistance Required \| Blocked \| Completed |
| **Assignee** | <Agent ID or Human ID from config.md> |
| **Estimate** | S (1 day) \| M (2-3 days) \| L (1 week) |
| **Depends on** | <Ticket ID or '—'> |
| **Blocks** | <Ticket ID or '—'> |
| **Labels** | `backend`, `sql`, `api` |

## Summary
Concise description of the task, business motivation, and expected outcome.

## Requirements & Scope / Technical Specification
Detailed technical design, file boundaries, verified schemas, and edge cases.

## Acceptance Criteria
- [ ] Requirements specified and grounded against wiki/schemas
- [ ] Implemented following `phase_build.md`
- [ ] Verified with real tests and artifacts saved in `./Verification/`

## Work Log
- **YYYY-MM-DD**: Claimed ticket. Grounded requirements and drafted implementation plan.
```

---

## 4. Standard Agent Operating Protocol (7 Steps)

1. **Discover & Check Dependencies:** Inspect `Tickets/Backlog/Ready/` (or `Backlog/`). Verify every ticket listed in `| **Depends on** |` is already in `Tickets/Completed/`. If any prerequisite is unfinished, move the ticket to `Tickets/Blocked/` and log why.
2. **Claim the Ticket:**
   ```bash
   git mv "Tickets/Backlog/Ready/PAY-001_schema.md" "Tickets/Ongoing/PAY-001_schema.md"
   ```
   Update `| **Status** | Ongoing |` and `| **Assignee** | <Your Agent ID> |`, and append a dated `## Work Log` entry.
3. **Specify Requirements (if incomplete):** Fill in `## Summary`, technical scope, and `- [ ]` checkboxes before coding.
4. **Implement within Bounded Scope:** Follow `phases/phase_build.md`.
5. **Verify & Test Rigorously:** Follow `phases/phase_finalize.md`. Tick `- [x]` checkboxes only as each criterion is proven.
6. **Handle Blockers & Assistance:**
   * Need human decision, architecture sign-off, or credentials → `git mv` to `Tickets/Assistance Required/`, set `Status: Assistance Required`, and log the exact question.
   * Blocked by external dependency → `git mv` to `Tickets/Blocked/`, set `Status: Blocked`, and log the blocker.
7. **Finalize & Complete:**
   ```bash
   git mv "Tickets/Ongoing/PAY-001_schema.md" "Tickets/Completed/PAY-001_schema.md"
   ```
   Set `| **Status** | Completed |`, verify all `- [x]` boxes are checked, append final `## Work Log` entry, and update milestone status in `Tickets/00_Plan.md`.

---

## 5. Multi-Model Orchestration (`Tickets/config.md`)

| Model Tier | Cost Profile | Recommended Categories | Example Models |
|---|---|---|---|
| `fast-discovery` | Free / Low | `discovery`, `quick-fix`, grep/index lookups, symbol checks | Local Ollama (`gemma4:e4b`), Haiku |
| `standard-coder` | Medium | `implementation`, `verification`, `refactor`, test writing | Sonnet, Devin, Copilot, Gemini Flash |
| `deep-reasoner` | High | `architecture`, `escalation`, complex cross-module debugging | Pro / Thinking / Deep Reasoning models |

---

## 6. Critical Invariants

1. **Never Silently Overwrite Human WIP** — check `git status` first.
2. **Work Logs are Append-Only** — never erase or rewrite previous `## Work Log` entries.
3. **Standard Checkboxes Only (`- [ ]` / `- [x]`)** — drives the UI progress bar badge.
4. **Use `git mv` for Column Transitions** — preserves file history and avoids ghost cards.
5. **Keep Folder Location and `| **Status** |` in Sync.**
6. **Never Delete `Whatwhy.md`, `00_Plan.md`, or `config.md`.**
