# Skill: API & Contract Testing, Bug Triage & Tool Taxonomies (Startup Edition)

> **Load When:** Testing REST/GraphQL/RPC endpoints, triaging bugs vs. product questions, or exposing large API/tool catalogs to AI agents.

---

## 1. Four Rules for API / Contract Findings
1. **Plain-Language Description + Reproducing Test:** State what was called, with what input, what happened, and what was expected — linked to a real reproducing test.
2. **Compare API vs. Internal Function & Schema:** Check whether the discrepancy is in the HTTP/API adapter or the underlying domain function/schema (`API bug` vs. `possibly intended — RQ-NNN`).
3. **Append-Only Triage Notes:** Never overwrite prior bug notes; append dated updates (`**Update YYYY-MM-DD:**`).
4. **Check Existing Tickets First:** Check `Tickets/` (or GitHub Issues) before creating a duplicate card.

## 2. Large Tool / Endpoint Surface Safety
When giving an agent a large API or command list, classify actions by pattern into:
* 🟢 `AUTONOMOUS` (read-only `GET`/`list`), 🟡 `LOGGED_WRITE` (local dev only), 🟠 `CONFIRMATION_REQUIRED` (mutations), and ⛔ `DO_NOT_USE` (destructive deletes / live payouts).
* Strip bulky raw JSON responses after assertions and save full dumps to `./Verification/`.
