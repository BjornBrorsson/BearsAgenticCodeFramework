# Phase: PLAN & DESIGN — Solution Architecture, Critical Questioning & Pre-Mortem (Enterprise Edition)

> **Gatekeeper:** Read this file in full when designing a new feature, service, migration, or integration, or whenever the user asks to **plan, architect, or design** a solution before building.
> **Core Rule:** **DO NOT write production code in this phase.** Act as a critical solution architect: challenge assumptions, ask high-signal clarifying questions, run a Pre-Mortem to catch pitfalls before they happen, and wait for user sign-off on the plan.
> *(Load `../skills/architecture_and_premortem.md` for templates.)*

---

## 1. The 6-Step Planning & Architecture Loop

```text
1. GROUND REALITY → 2. INTERROGATE GAPS → 3. TRADE-OFF OPTIONS → 4. ARCHITECTURE & DATA DESIGN → 5. PRE-MORTEM (PITFALLS) → 6. PLAN & SIGN-OFF GATE
```

1. **Ground Reality (`ORIENT`):** Check `./docs/DOCUMENTATION_INDEX.md`, `./docs/wiki/<domain>.md`, `./docs/MASTER_MEMORIES.md`, and `./docs/schemas/SCHEMA_INDEX.md` first. Check if existing modules or helpers already solve 80%+ of the requirement ("Less Code is Better").
2. **Interrogate Gaps (Ask What Matters):** Ask concise, numbered questions (with recommended defaults) covering:
   * **Business & Financial Invariants:** Rounding, negative/credit amounts, currency/locale rules, state transitions.
   * **Trigger, Auth & Idempotency:** Who calls it, what permissions apply, and what happens if triggered twice?
   * **Volume, Concurrency & SLA:** Expected row/request volume and concurrent execution behavior.
   * **Failure & Recovery Semantics:** Full batch rollback vs. per-item dead-letter/retry.
3. **Present Trade-Off Options:** Compare Option A (Recommended — minimal code/reuse) vs. Option B (Custom/alternative) across maintenance cost, performance, and lock-in risk.
4. **Design the Solution Architecture (`[TASK_ID]_Specs.md`):**
   * End-to-end Mermaid sequence/flow diagram.
   * Exact file, service, config key (`[PREFIX]__<Name>`), and test inventory.
   * Verified schema contract (`SCHEMA_INDEX.md`) + transaction boundaries + rollback design.
5. **Run a Mandatory "Pre-Mortem" ("Why Did This Fail at 02:00 AM?"):**
   Explicitly list the top 5 likely failure modes (duplicate runs, 0-row/0-byte inputs, null/casing mismatches, lock escalation/unindexed queries, external API timeouts, branch drift) and bake their guardrails into the spec.
6. **Decompose & Wait for Sign-Off:**
   Break work into testable steps in `[TASK_ID]_Specs.md` (or `Tickets/Backlog/Ready/` cards if >3 subtasks per `../skills/kanban_workflow.md`). **STOP and wait for user approval** before entering `phase_build.md`.
