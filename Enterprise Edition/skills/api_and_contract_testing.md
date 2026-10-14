# Skill: API & Contract Testing, Defect Triage & Tool Taxonomies (Enterprise Edition)

> **Load When:** Probing REST, GraphQL, SOAP, or RPC endpoints, triaging automated test findings, comparing external API behavior against internal handlers/schemas, or exposing large API/command catalogs to AI agents.
> **Companion Templates:** `C:\Aptic\Tools\AgentToolTaxonomyTemplates\REST_API_Tool_Taxonomy_Template.md` and `RPC_Tool_Taxonomy_Template.md`.

---

## 1. The Four Mandatory Defect & Contract Triage Rules

1. **Clear Plain-Language Description + Reproducing Testcase Link:**
   * Start every finding with a clear description a non-developer can understand: what was called, with what input, what happened, and what should have happened.
   * Link to the exact reproducing test file and run/test ID. *A finding without a reproducing test is a hypothesis, not a finding.*
2. **Compare API Behavior Against Schema AND Internal Non-API Counterparts:**
   * Check the declared contract (OpenAPI, GraphQL schema, XSD, `./docs/schemas/SCHEMA_INDEX.md`) AND test the same operation via the internal service/RPC/UI path:
     * **API differs from internal path** → **API Bug**.
     * **API and internal path behave the same** → **Possibly intended behavior** (log an open rulings question `RQ-NNN` for product clarification, or document why both share a core bug).
3. **Append-Only Triage Logs (Never Overwrite):**
   * Never delete or overwrite previously reported findings in triage reports or rulings files. Append new evidence with `**Update YYYY-MM-DD (source/run):** …` and never reuse defect/question IDs.
4. **Check the Issue Tracker for Duplicates First:**
   * Query the issue tracker (Jira / Azure DevOps / TFS / GitHub Issues) for existing open or closed tickets matching the endpoint or error message before drafting a new bug report. Record the matched issue ID (or `"none open — searched <terms> on YYYY-MM-DD"`).

---

## 2. Paste-Ready Defect / Rulings Finding Template

```markdown
### <Defect N | RQ-NNN>: <Short statement>
- **Description:** <Plain language: call, input, observed, expected>
- **Testcase:** `<path/to/test>` (run `<run_id>`, test `<test_id>`)
- **Surface:** `<HTTP verb /path | RPC command>` · **Version/Build:** `<vX.Y.Z>`
- **API vs Internal / Schema:** `<internal handler result + schema contract>` → `<API bug | same in both: possibly intended (RQ-NNN) | pending>`
- **Known in Issue Tracker:** `<#id state title | none open (terms, YYYY-MM-DD)>`
- **Expected / Actual / Evidence (`./Verification/`) / Business Impact**
```

---

## 3. Large API / RPC Surface Safety (Agent Tool Taxonomies)

When handing a large catalog of endpoints or RPC commands to an LLM agent, prepend the 8-part **Agent Configuration Taxonomy**:
1. **Layered Action Space:** `CORE` (always in context), `DORMANT` (domain-loaded), `JIT` (on-demand).
2. **Danger Classification:**
   * 🟢 `AUTONOMOUS` — Read-only queries (`GET`, `*_get`, `*_list`).
   * 🟡 `LOGGED_WRITE` — Local disposable sandbox state setup.
   * 🟠 `CONFIRMATION_REQUIRED` — State mutations requiring explicit user confirmation.
   * ⛔ `DO_NOT_USE` — Destructive, financial-payout, or production-delete operations (`DELETE`, `*_drop`, `*_purge`).
3. **Pattern-Based Rules:** Derive danger tier automatically from HTTP verbs or command suffixes (`_get` vs `_delete`) rather than manual per-row tags.
4. **Context Compaction:** Strip raw bulky responses after assertions and save full payloads to `./Verification/`.
