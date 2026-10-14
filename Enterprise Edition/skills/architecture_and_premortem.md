# Skill: Solution Architecture, Critical Questioning & Pre-Mortem (Enterprise Edition)

> **Load When:** Operating in `phases/phase_plan_and_design.md` — designing a solution, surfacing hidden requirements, comparing trade-offs, or running a Pre-Mortem risk analysis before coding.

---

## 1. High-Signal Clarifying Questions Checklist

Before locking an architecture, verify and ask about:
1. **Business & Financial Invariants:** Rounding rules, positive vs. negative/credit amounts, multi-currency/multi-region rules, and state machine transitions.
2. **Trigger, Auth & Idempotency:** What triggers the flow, what roles/permissions are required, and what guarantees idempotency if the job/request runs twice?
3. **Volume, Concurrency & SLA:** Expected row/request volume and concurrent execution behavior.
4. **Partial Failure & Rollback:** Should a single record failure roll back the entire batch or route to a dead-letter/retry state?

---

## 2. Pre-Mortem Risk Matrix Template (`[TASK_ID]_Specs.md`)

```markdown
## Pre-Mortem Risk Analysis ("How Could This Fail in Production?")

| # | Failure Mode / Pitfall | Likelihood | Impact | Design Guardrail Built Into Plan |
|---|---|---|---|---|
| 1 | Duplicate execution processes the same records twice | Med | High | Idempotency key / processed-status check inside transaction |
| 2 | 0-row query or 0-byte input file causes unhandled null error | High | Med | Pre-flight check + `[WARNING] Query returned 0 rows` + clean exit |
| 3 | Unindexed query on large table causes lock escalation / timeout | Med | High | Filter on indexed keys; batch mutations (`TOP 2000`) |
| 4 | External API timeout or encoding mismatch (`UTF-8`) | Med | Med | Explicit UTF-8 encoding, timeout, structured `try-catch` + `[ERROR]` log |
```
