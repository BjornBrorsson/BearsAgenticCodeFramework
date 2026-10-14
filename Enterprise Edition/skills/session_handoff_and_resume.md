# Skill: Session Handoff, Context Compaction & Multi-Agent Resume (Enterprise Edition)

> **Load When:** Pausing or ending a session on an unfinished task, approaching context limits, or handing work between agents/developers.

---

## 4-Part Handoff Protocol

1. **Update `PROJECT_LOG.md`:** Record every file changed so far, what is verified, and what remains.
2. **Update `PROJECT_MEMORIES.md`:** Save discovered schema facts, config keys, and component quirks so the next session skips repeat lookups.
3. **Sync the Kanban Card (`Tickets/`):** Ensure the card in `Tickets/Ongoing/` (or `Assistance Required/` / `Blocked/`) has completed `- [x]` boxes ticked and a dated `## Work Log` entry.
4. **Write `./tasks/<task>/HANDOFF_STATE.md`:**
   * **Active Phase & Resume Point:** Exact file and symbol (`~Line`) currently in progress.
   * **Completed vs. Remaining Checklist.**
   * **Open Questions / Blockers.**
   * **3 Files the Next Agent Must Read First.**
