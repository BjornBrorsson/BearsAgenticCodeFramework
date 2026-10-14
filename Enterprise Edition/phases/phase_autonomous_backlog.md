# Phase: AUTONOMOUS BACKLOG — V1 Gap Specification & Unattended Backlog Drain (Enterprise Edition)

> **Gatekeeper:** Read this file in full whenever the user gives an **autonomous batch or unattended directive**:
> * **Mode A (Bulk Spec / V1 Gap):** *"Specify tickets for everything left to reach V1"*, *"Break down the roadmap into ready tickets"*, or *"Finish specifying the backlog"*.
> * **Mode B (Unattended Backlog Drain):** *"Complete as many tickets as you can — goal is an empty backlog"*, or *"Work through the Ready queue while I'm away"*.
>
> **Companion Skills:** Load `../skills/kanban_workflow.md`, `../skills/subagent_orchestration.md`, and `../skills/session_handoff_and_resume.md`.

---

## 1. The Unattended Golden Rule (Never Stall the Whole Queue on One Blocked Ticket)

In an unattended batch run, if Ticket #N hits a §1.1 Stop-and-Ask condition, requires human credentials/decisions, or has an unresolvable ambiguity:
1. Revert any partial uncommitted code changes from Ticket #N so the working tree stays 100% clean.
2. Move Ticket #N via `git mv` to `Tickets/Assistance Required/` (or `Tickets/Blocked/` if waiting on another ticket/system).
3. Set `| **Status** | Assistance Required |` and write the **exact question, options, and recommendation** in the ticket's `## Work Log`.
4. Commit the board update and **immediately move on to Ticket #N+1** in `Tickets/Backlog/Ready/`!

---

## 2. Mode A — "Specify Everything Left to Reach V1" (V1 Gap-to-Backlog Protocol)

1. **Audit Target Vision vs. Current Reality:**
   * Compare `Tickets/00_Plan.md` and `[TASK_ID]_Specs.md` against `Tickets/Completed/`, `Tickets/Ongoing/`, and the actual `./src/` codebase.
   * *(If sub-agents are supported)* Spawn read-only exploration sub-agents in parallel to audit separate modules against the V1 target.
2. **Compute the 6-Layer V1 Gap Matrix:**
   Check for missing work across: (1) Database/Migrations, (2) Core Domain/Services/APIs, (3) Config & Auth/Security, (4) UI/Reports/Integrations, (5) Automated Tests & Verification, and (6) Deployment Manifest & Runbooks.
3. **Author Atomic, Self-Contained Tickets (`Estimate: S` or `M`):**
   * Include full metadata table (`Epic`, `Type`, `Priority`, `Status`, `Assignee`, `Estimate`, `Depends on`, `Blocks`, `Labels`).
   * Include grounded `## Technical Specification` with exact file paths, verified schema columns (`SCHEMA_INDEX.md`), and Durable Deep Links (`Symbol` + `~L45` + `_Verified: YYYY-MM-DD._`).
   * Include binary `- [ ]` **Acceptance Criteria** (Happy path, 0-row/null, boundary/UTF-8, error/rollback).
4. **Triage `Ready/` vs. `Needs further specification/`:**
   * Fully specified → `Tickets/Backlog/Ready/`.
   * Needs a human product/business decision → `Tickets/Backlog/Needs further specification/` with an explicit `## Open Questions` block at top.

---

## 3. Mode B — "Complete As Many Tickets As Possible" (Unattended Backlog Drain Loop)

1. **Dependency & Priority Ordering:** Pick only tickets in `Tickets/Backlog/Ready/` whose `| **Depends on** |` prerequisites are all in `Tickets/Completed/`, ordered `Critical/P0 → High/P1 → Normal/P2 → Low/P3`.
2. **Per-Ticket Micro-Loop:**
   * Claim (`git mv` → `Tickets/Ongoing/`, set `Status: Ongoing`, add dated `## Work Log` entry).
   * Execute `phase_build.md` → `phase_test_and_validate.md` (self-healing test loop + `./Verification/<TicketID>/` proof) → `phase_finalize.md`.
   * Complete (`git mv` → `Tickets/Completed/`, set `Status: Completed`, check all `- [x]`, update `PROJECT_LOG.md`).
3. **Atomic Git Checkpoint After Each Completed Ticket:**
   * Commit each finished ticket cleanly (`feat(<TicketID>): <summary>`) before starting the next ticket so completed work is never lost if a later ticket blocks.
4. **Use Sub-Agents to Prevent Context Rot:** Offload per-ticket exploration or independent non-overlapping tickets to sub-agents (`../skills/subagent_orchestration.md`) and update `HANDOFF_STATE.md` after every ticket.
5. **Final Unattended Run Summary:** Report a scoreboard table of `Completed (N)`, `Moved to Assistance Required (M — with exact questions)`, `Moved to Blocked (K)`, and `Remaining`.
