# Phase: INVESTIGATE — "How Does X Work?" & "Why Does Y Behave Like This?" (Enterprise Edition)

> **Gatekeeper:** Read this file in full whenever the user asks an **explanatory, architectural, or behavioral question** — e.g., *"How does X work?"*, *"Why is Y not working like this or that?"*, *"Where is Z calculated?"*, or *"What happens when endpoint W is called?"*
>
> **Key Difference from `phase_research.md`:**
> Pure investigation does **NOT** require requesting the 3 task trackers (`Component`, `Task ID`, `Task Name`) or creating a `./tasks/<task>/` folder upfront. Answer the question directly without administrative friction!
> *(If the user later asks to implement a change based on the findings, transition to `phase_research.md` §2 to isolate and scaffold before editing code.)*

---

## 1. The 5-Step Investigation Loop

```text
1. ORIENT (Curated KB) → 2. TRACE END-TO-END → 3. EXPLAIN THE "WHY" (Delta Analysis) → 4. CITE DURABLE LINKS → 5. CAPTURE KNOWLEDGE
```

### Step 1 — ORIENT: Check Curated Sources Before Repo-Wide Search
1. `./docs/DOCUMENTATION_INDEX.md` — locate the owning directory and domain page.
2. `./docs/wiki/<domain>.md` — check existing architecture flows and source links.
3. `./docs/MASTER_MEMORIES.md` — check if this exact quirk or behavior is already documented.
4. `./docs/schemas/SCHEMA_INDEX.md` — verify table, column, and payload definitions.

### Step 2 — TRACE END-TO-END: Follow the Real Code & Data Path (Zero Guessing)
Trace the actual execution path across all layers rather than guessing from names:
1. **Entry Point:** UI component, API route/controller, CLI command, or scheduled job.
2. **Domain / Service Layer:** Business rules, validation guards, feature flags, and middleware.
3. **Core Engine / Library Layer:** Underlying framework or shared core behavior.
4. **Data & Integration Layer:** SQL tables/views/triggers, ORM models, external API contracts, and live DB config state (`SELECT`-only with non-locking reads `WITH (NOLOCK)` / `READ UNCOMMITTED`).

### Step 3 — EXPLAIN "WHY Y DOES NOT WORK LIKE THAT" (Delta Analysis)
Contrast the **Expected Mental Model vs. Actual Implementation**:
* Identify the exact branch condition, config flag, permission gate, schema constraint, serialization rule, or framework quirk responsible for the behavior.
* When comparing two surfaces (e.g. REST API vs. internal handler/UI), show side-by-side where their code paths diverge.

### Step 4 — PRESENT THE ANSWER (Structured + Durable Deep Links)
1. **TL;DR Summary:** 2–4 sentences answering *how* or *why* directly.
2. **Execution Flow:** `[Entry Point] → [Service/Handler] → [Core/DB]`.
3. **Key Gates & Invariants:** The exact conditions governing the behavior.
4. **Verified Source Map:** Symbol-anchored Durable Deep Links (`SymbolName()` in `path/to/file` (`~L45`). `_Verified: YYYY-MM-DD._`).

### Step 5 — CAPTURE KNOWLEDGE (Don't Let Findings Disappear in Chat)
* If the investigation uncovered undocumented domain flows → update `./docs/wiki/<domain>.md` (anonymized, with Durable Deep Links).
* If it uncovered a non-obvious framework/API/DB gotcha (>30 min or Two-Strike) → log it in `./docs/MASTER_MEMORIES.md`.
* If a change or fix is needed next, state the recommended options and offer to transition to `phase_research.md` / `phase_build.md`.
