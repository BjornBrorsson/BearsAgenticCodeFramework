# Phase: PLAN & DESIGN — Architecture, Critical Questioning & Pre-Mortem (Startup Edition)

> **Gatekeeper:** Read this file in full when planning a feature, designing an architecture, or before building a non-trivial change.
> **Core Rule:** **Do not write code yet.** Think critically, challenge bad assumptions, ask the questions that need answers, run a quick Pre-Mortem, and wait for user approval.
> *(Load `../skills/architecture_and_premortem.md` for templates.)*

---

## 5-Step Planning Loop

1. **Ground First:** Check `./docs/DOCUMENTATION_INDEX.md`, `./docs/wiki/`, and `./docs/MASTER_MEMORIES.md`. Can we reuse an existing helper or pattern ("less code is better")?
2. **Ask What Matters:** Ask concise, numbered questions (with sensible default recommendations) about edge cases, auth/permissions, data volume, idempotency (what if it runs twice?), and failure recovery.
3. **Compare Options:** Briefly contrast Option A (Recommended — simplest/cleanest) vs. Option B (Alternative) on speed, maintenance, and lock-in.
4. **Sketch Architecture & Pre-Mortem:** Draw a quick Mermaid flow, list touched files/tables, and list the top 3–5 ways this could break in production ("Pre-Mortem") + how the design prevents them.
5. **Break into Tasks & Get Sign-Off:** If >3 subtasks, create cards in `Tickets/Backlog/Ready/` (`../skills/kanban_workflow.md`). **Wait for user go-ahead** before entering `phase_build.md`.
