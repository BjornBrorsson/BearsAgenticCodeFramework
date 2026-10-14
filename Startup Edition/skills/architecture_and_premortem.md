# Skill: Architecture, Critical Questioning & Pre-Mortem (Startup Edition)

> **Load When:** Operating in `phases/phase_plan_and_design.md` — planning a feature, challenging assumptions, or catching pitfalls before coding.

---

## 1. Questions to Ask Before Coding
1. **Edge Cases & State:** What happens on empty/0-row inputs, negative/zero amounts, or duplicate requests (idempotency)?
2. **Auth & Permissions:** Who is allowed to call this, and what happens on unauthorized access?
3. **Failure Handling:** If an external API or DB write fails mid-way, how do we roll back or retry cleanly?

## 2. Quick Pre-Mortem Table ("How Could This Break?")
| # | Potential Pitfall | Prevention Built Into Plan |
|---|---|---|
| 1 | Duplicate webhook / double-click submits twice | Idempotency key / unique constraint |
| 2 | Empty result / null field crashes parser | Pre-flight guard + `[WARNING]` log |
| 3 | External API timeout / UTF-8 encoding issue | Timeout + `try-catch` + explicit UTF-8 |
