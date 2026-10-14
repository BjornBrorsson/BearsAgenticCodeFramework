# Skill: Knowledge Wiki, Master Memories & Durable Deep Links (Startup Edition)

> **Load When:** Updating `./docs/wiki/<feature>.md`, `./docs/MASTER_MEMORIES.md`, or `./docs/DOCUMENTATION_INDEX.md`.

---

## 1. Knowledge Routing (One Home Per Finding)

| What You Learned | Where It Goes |
|---|---|
| What changed this task + 1-line post-mortem + rollback | `./tasks/<feature>/PROJECT_LOG.md` |
| A note or quirk specific to this feature only | `./tasks/<feature>/PROJECT_MEMORIES.md` |
| How the feature works + Durable Deep Links to code | `./docs/wiki/<feature>.md` |
| A reusable bug/quirk any feature could hit (Two-Strike or >30 min) | `./docs/MASTER_MEMORIES.md` |
| A new folder, tool, or doc worth knowing | `./docs/DOCUMENTATION_INDEX.md` |

## 2. Durable Deep Links (Never Use Bare Line Numbers)
1. **Anchor on the symbol name** (`handleStripeWebhook()`), not a line range.
2. **Line is a hint only:** `~L42`.
3. **Date-stamp:** `_Verified: YYYY-MM-DD._`
4. **Repair on touch:** Update the link and date whenever you edit the linked file.
* *Example:* `` **Webhook Handler** — `handleStripeWebhook()` in [src/routes/billing.ts](../../src/routes/billing.ts) (~L42). _Verified: 2026-10-13._ ``
