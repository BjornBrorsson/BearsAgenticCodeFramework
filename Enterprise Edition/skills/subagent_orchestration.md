# Skill: Sub-Agent Orchestration & Parallel Execution (Enterprise Edition)

> **Load When:** The active AI agent runtime supports spawning sub-agents and the task involves broad codebase exploration, multi-module audits, bulk V1 ticket specification, or parallel independent workstreams.

---

## 1. When to Use Sub-Agents

* **Context Window Protection:** Offload broad searches, multi-file tracing, and schema/wiki audits to read-only explorer sub-agents so only a concise 20-line synthesis + Durable Deep Links enters the main context.
* **Parallel Execution:** Run independent module audits, bulk ticket specifications, or non-overlapping file implementations in parallel sub-agents.
* **Do It Directly When:** Reading 1–3 known files or editing tightly coupled files sequentially.

---

## 2. Four Mandatory Rules for Stateless Sub-Agents

1. **Front-Load Safety Guardrails (§1) & Context:** Sub-agents cannot see your chat history. Always include in every sub-agent prompt:
   * `SELECT`-only non-locking DB reads; never run DML/DDL on shared/prod DBs.
   * Read-only core directories; zero hardcoded secrets; client/tenant anonymization.
   * Format all code citations as Durable Deep Links (`SymbolName` in `<path>` (`~L45`). `_Verified: YYYY-MM-DD._`).
2. **Strict Disjoint File & Ticket Ownership (Zero Collisions):**
   * Never allow two parallel sub-agents to edit the same file, claim the same Kanban ticket, or write to `PROJECT_LOG.md` simultaneously. Assign explicit disjoint file lists and let the main agent merge updates into `PROJECT_LOG.md` and `[TASK_ID]_Specs.md`.
3. **State "Investigate Only" vs. "Make Changes" Explicitly.**
4. **Require Structured Return Output:** Summary of findings/changes, verified Durable Deep Links, `./Verification/` evidence paths, and any stop-gate blockers encountered.
