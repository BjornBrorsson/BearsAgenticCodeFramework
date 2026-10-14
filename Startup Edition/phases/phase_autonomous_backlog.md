# Phase: AUTONOMOUS BACKLOG — V1 Gap Specification & Unattended Backlog Drain (Startup Edition)

> **Gatekeeper:** Read this file in full when given an **autonomous batch or unattended directive**:
> * **Mode A (V1 Gap Spec):** *"Speca tickets för allt som är kvar till V1"* / *"Break down what's left into ready tickets"*.
> * **Mode B (Unattended Backlog Drain):** *"Slutför så många tickets du kan — målet är en tom backlog"* / *"Clear the Ready queue while I'm away"*.
> **Companion Skills:** Load `../skills/kanban_workflow.md`, `../skills/subagent_orchestration.md`, and `../skills/session_handoff_and_resume.md`.

---

## 1. The Unattended Golden Rule (Never Stall the Queue on One Blocked Card)

If Card #N hits a blocker, needs an API key/credential, or requires a product decision from the human:
1. Revert any partial uncommitted code from Card #N so `git status` is clean.
2. `git mv` Card #N to `Tickets/Assistance Required/` (or `Tickets/Blocked/` if waiting on another card).
3. Set `Status: Assistance Required` and write the **exact question + your recommended default** in `## Work Log`.
4. Commit the board move and **immediately continue to Card #N+1** in `Tickets/Backlog/Ready/`!

---

## 2. Mode A — "Specify Everything Left to Reach V1"

1. **Audit `Tickets/00_Plan.md` vs. `./src/` & `Tickets/Completed/`:** Identify every missing feature, DB migration, error handler, test, and deploy step needed for V1 (use parallel explorer sub-agents if available).
2. **Create Atomic Cards (`Estimate: S` or `M`):** Give each card a clear `## Summary`, exact file/model pointers (with Durable Deep Links), `| **Depends on** |` links, and binary `- [ ]` **Acceptance Criteria**.
3. **Route Cleanly:** Put 100% clear cards in `Tickets/Backlog/Ready/` and cards with open product questions in `Tickets/Backlog/Needs further specification/` (with the questions listed at top).

---

## 3. Mode B — "Complete As Many Tickets As Possible" (Queue Runner)

1. **Order by Priority & Dependencies:** Pick from `Tickets/Backlog/Ready/` (`Critical → High → Normal → Low`) where all `Depends on` cards are in `Tickets/Completed/`.
2. **Full Per-Card Loop:** Claim (`git mv` → `Ongoing/`) → Build (`phase_build.md`) → Self-Heal & Validate (`phase_test_and_validate.md`, saving proof in `./Verification/<CardID>/`) → Complete (`git mv` → `Completed/`, tick all `- [x]`).
3. **Git Commit Checkpoint Per Card:** Commit after **every** completed card (`feat(<CardID>): <summary>`) so finished cards are safely saved before starting the next one.
4. **Prevent Context Rot:** Use sub-agents (`../skills/subagent_orchestration.md`) for heavy exploration or disjoint cards, and update `HANDOFF_STATE.md` after each card.
5. **Final Scoreboard Report:** Summarize `✅ Completed (N)`, `🙋 Assistance Required (M — with exact questions)`, and `⏸️ Blocked (K)`.
