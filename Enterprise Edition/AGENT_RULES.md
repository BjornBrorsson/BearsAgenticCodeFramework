# Agentic Workspace Rules — Core, Guardrails & Phase Router (Enterprise Edition)

> **Architecture (Progressive Disclosure):** To prevent instruction dilution in long sessions, this file is the **always-loaded core**. It holds only non-negotiable principles, safety/privacy guardrails (§1), and the **Phase & Skill Router (§2)**. Phase- and task-specific rules live in `./phases/` and `./skills/` and **must** be read on-demand before entering each phase.
>
> **Companion Documents:** `workspace_architecture_blueprint.md` (architectural rationale & templates) and `agentic_rules_blueprint.md` (monolithic reference).

---

## 0. Core Principles & Path Conventions

* **Core Principle:** Stability, security, and predictability *always* take precedence over speed or convenience.
* **Precedence (on conflict):** `Safety & Data Privacy (§1)` → `explicit user instructions` → `this file & loaded phase/skill files` → `agent defaults`. Everything except §1 safety rules is an overridable default; when you override one, explicitly state what you overrode and why.
* **Less Code is Better Than More Code:** Every line of code must be maintained. Reuse existing patterns, modules, and helpers from `./src/` and prior `./tasks/` solutions whenever possible. Keep code compact and neat, and follow the repository's existing conventions over the defaults in this ruleset.
* **Sub-Agents Encouraged Where Appropriate:** When your agent runtime supports sub-agents, actively use them to offload broad codebase/schema searches, parallel module audits, bulk ticket specification, and independent non-overlapping tasks so the main context stays focused. Always pass §1 safety guardrails in sub-agent prompts and never let two parallel sub-agents edit the same file (see `skills/subagent_orchestration.md`).
* **Path Convention:** All paths are relative to the repository root (`./docs/`, `./src/`, `./tasks/`, `./Tickets/`, `./tools/`).

---

## Core Loop (Every Session)

Every task performed in this repository cycles systematically through six phases:

> **ORIENT → ISOLATE → GROUND → BUILD → VERIFY → RECORD**

| Loop Step | Core Contract | Mandatory Phase File | Recommended Skills (Load When Applicable) |
|---|---|---|---|
| **1. ORIENT** | Read `./docs/DOCUMENTATION_INDEX.md`, the relevant `./docs/wiki/<domain>.md` page, `./docs/MASTER_MEMORIES.md`, and `./docs/schemas/SCHEMA_INDEX.md` before searching code. (If missing, bootstrap per `skills/project_kickoff.md`.) | `phases/phase_research.md` *(or `phase_investigate.md` / `phase_teach.md`)* | `knowledge_wiki_and_memories.md`, `subagent_orchestration.md` |
| **2. ISOLATE** | Request task metadata (**Component/Client**, **Task/Ticket ID**, **Task Name**), create `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/`, and seed `[TASK_ID]_Specs.md`. | `phases/phase_research.md` *(or `phase_autonomous_backlog.md`)* | `project_kickoff.md`, `kanban_workflow.md`, `conventions_and_naming.md`, `requirements_and_spec_writing.md` |
| **3. GROUND** | Trace `requirement → wiki/module → config → code → data` before editing. Verify schemas, API contracts, and column types; cite reused sources. | `phases/phase_research.md` *(or `phase_plan_and_design.md`)* | `architecture_and_premortem.md`, `database_change_scripts.md`, `security_privacy_and_compliance.md` |
| **4. BUILD** | SELECT-only DB access; secrets via env/placeholders; wrap all I/O, DB, and API calls in structured error handling and `[YYYY-MM-DD HH:MM:SS] [SEVERITY] [Component] - Message` logging; run pre-flight checks. | `phases/phase_build.md` *(or `phase_refactor.md`)* | `conventions_and_naming.md`, `database_change_scripts.md`, `performance_and_query_tuning.md` |
| **5. VERIFY** | Never write placeholder tests (`assert(true)`); dry-run locally; test boundaries; wrap local DB write tests in `BEGIN TRANSACTION … ROLLBACK TRANSACTION`; save manual review artifacts in `./Verification/`. | `phases/phase_test_and_validate.md` & `phases/phase_finalize.md` *(or `phase_review.md`)* | `autonomous_validation_and_artifacts.md`, `local_testing.md`, `api_and_contract_testing.md` |
| **6. RECORD** | Update `PROJECT_LOG.md` (with post-mortem & rollback) and `PROJECT_MEMORIES.md`; update `./docs/wiki/<domain>.md` (with verified deep links); promote reusable gotchas to `./docs/MASTER_MEMORIES.md`. | `phases/phase_finalize.md` | `knowledge_wiki_and_memories.md`, `git_and_release_delivery.md`, `session_handoff_and_resume.md` |

---

## 1. Safety, Environment & Data Guardrails (NON-NEGOTIABLE)

Treat every environment as production-like unless explicitly designated as an isolated local disposable sandbox. Adhere to organizational information security policies (ISO/IEC 27001 / SOC 2 controls).

### 1.1 When to Stop and Ask

Proceed by default and record assumptions in the spec/log, but **STOP and ask** the user for explicit confirmation when:

* The request is ambiguous and materially affects database state, financial calculations, security boundaries, or external system integrations.
* The operation is destructive or irreversible: modifying production data, dropping/altering tables, deleting files you did not create, rewriting git history, force-pushing, or sending real external communications/payments.
* The choice introduces expensive lock-in (e.g., adding a new external dependency, running a schema migration, changing a public API contract).
* Continuing would require violating any guardrail in §1.

### 1.2 Database Guardrails (SELECT Only)

* **NEVER** execute `UPDATE`, `INSERT`, `DELETE`, `MERGE`, `ALTER`, `TRUNCATE`, `DROP`, or other DML/DDL directly on any shared, staging, or production database.
* **Local Sandbox Exception:** Disposable localhost dev databases may execute DML/DDL for local verification, provided non-persisted tests are wrapped in `BEGIN TRANSACTION … ROLLBACK TRANSACTION`.
* **Scripts-as-Text for All Other Environments:** Deliver all change/migration/data-fix scripts as reviewable `.sql` files in `./tasks/<task>/` with the note:
  > *"Here is the script. Please review it carefully and execute it yourself."*
* **Mandatory Non-Locking Reads:** Any direct AI-executed diagnostic query **MUST** use non-locking read semantics (e.g. `WITH (NOLOCK)` / `READ UNCOMMITTED` or the database engine's equivalent).
* **Schema Verification First:** Always look up tables and payloads in `./docs/schemas/SCHEMA_INDEX.md` (or the actual schema/migration files) and verify column names, datatypes, and nullability before writing SQL.

### 1.3 Data Privacy, Tenant Isolation & Read-Only Core

* **CRITICAL — Client / Tenant Isolation:** **NEVER** leak or expose client-, tenant-, or customer-specific data, credentials, or proprietary business logic from one tenant/client customization into another. Generalize and anonymize all wiki pages, memories, and examples (replace identifiers with `"Client A"` or `"Tenant 001"`).
* **Read-Only Core:** Treat the stable core (e.g. `./src/core/` or `[CORE_LIBRARY_PATH]`) and published release branches as **read-only**. Do not modify them without explicit permission.

### 1.4 Files, Secrets & Outbound Communications

* **No Unapproved Deletions or Overwrites:** **NEVER** delete or overwrite existing files without explicit, per-case user consent. Draft to new, timestamped names or inside `./tasks/<task>/`, and archive superseded verification outputs to `./Verification/archive/`.
* **Mute Outbound Comms in Non-Prod:** Force email (`smtp`), SMS, push notifications, webhooks, and live payment gateways into test mode or disabled (`0` / `false`) in all non-production configurations.
* **Zero Hardcoded Secrets:** **NEVER** hardcode or commit API keys, passwords, tokens, or private keys. Use environment variables, secret stores, placeholders (`<YOUR_API_KEY>`), or integrated authentication.

---

## 2. Phase Router & Gatekeeper (MANDATORY)

### 2.1 The Gatekeeper Rule

1. **Mandatory On-Demand Read:** You **MUST NOT** enter a new development phase, write implementation files, or declare a task complete without first reading the mapped `phases/phase_*.md` file in full.
2. **Re-Read After Compaction:** If the session context was compacted, re-read the active phase file before continuing.
3. **Explicit Phase Signposting:** State active phase transitions (`[Phase: RESEARCH]`, `[Phase: BUILD]`, `[Phase: FINALIZE]`) so the user has visibility.
4. **Missing Module Stop:** If a mapped file is missing or unreadable, **STOP and tell the user** — never improvise a phase from memory.

### 2.2 Phase Modules (`./phases/`)

| Development Phase | Loop Steps Covered | Trigger — Read This File Before… | Module Path |
|---|---|---|---|
| **Research & Isolation** | `ORIENT`, `ISOLATE`, `GROUND` | Starting a development task, requesting trackers, creating `./tasks/<task>/`, or writing specs | `phases/phase_research.md` |
| **Plan & Architecture Design** | `ORIENT` → `GROUND` → `PRE-MORTEM` | Asking clarifying questions, designing solution architecture, comparing options, catching pitfalls before coding, and getting plan sign-off | `phases/phase_plan_and_design.md` |
| **Investigate & Explain** | `ORIENT` → `TRACE` → `RECORD` | Answering *"How does X work?"* or *"Why does Y behave like this?"* (no `./tasks/` scaffolding needed) | `phases/phase_investigate.md` |
| **Teach & Onboard** | `MODEL` → `FLOW` → `WALKTHROUGH` | Teaching how a domain/subsystem works from the ground up with diagrams, guided code tours, and exercises | `phases/phase_teach.md` |
| **Build & Robustness** | `BUILD` | Creating or modifying any code, SQL, script, service definition, or configuration | `phases/phase_build.md` |
| **Refactor (Golden Master)** | `BASELINE` → `SIMPLIFY` → `DIFF` | Simplifying, cleaning up, or modularizing existing code while preserving 100% behavior parity | `phases/phase_refactor.md` |
| **Test, Validate & Self-Heal** | `SPIN-UP` → `TEST` → `SELF-HEAL` | Spinning up local services, running the 4-boundary matrix, self-healing test failures, and creating `./Verification/` human review artifacts | `phases/phase_test_and_validate.md` |
| **Finalize, Deliver & Record** | `VERIFY`, `RECORD`, `DELIVER` | Archiving `./Verification/` evidence, updating logs/memories/wiki, preparing release manifests, or declaring "Done" | `phases/phase_finalize.md` |
| **Debug & Root-Cause** | `ORIENT` → `VERIFY` (Diagnostic) | Troubleshooting a bug, analyzing logs, comparing API vs internal handler behavior, or reproducing a failure | `phases/phase_debug.md` |
| **Code Review & Sign-Off** | `AUDIT` → `DRIFT` → `SIGN-OFF` | Auditing code, SQL scripts, PRs, checking branch/environment drift, and issuing a formal Sign-Off Verdict | `phases/phase_review.md` |
| **Autonomous Backlog / V1 Spec** | `GAP-SPEC` or `QUEUE-DRAIN` | Unattended batch runs: *"Specify everything left for V1"* (Mode A) or *"Complete as many tickets as possible"* (Mode B) | `phases/phase_autonomous_backlog.md` |
| **Hotfix / Incident Overlay** | All Steps (Compressed) | Handling an urgent production incident — read **in addition to** `phase_research`, `phase_build`, and `phase_finalize` | `phases/phase_hotfix.md` |

### 2.3 Skill Modules (`./skills/`)

| Task / Domain Trigger | Skill File to Read |
|---|---|
| Naming any file, script, DB table/view/trigger, parameter, or writing automated tests | `skills/conventions_and_naming.md` |
| Spawning sub-agents for parallel codebase exploration, audits, or disjoint tasks | `skills/subagent_orchestration.md` |
| Designing solution architecture, asking critical clarifying questions, comparing options, or running a Pre-Mortem | `skills/architecture_and_premortem.md` |
| Translating raw tickets, emails, or PRDs into testable specs (`[TASK_ID]_Specs.md`) & Kanban cards | `skills/requirements_and_spec_writing.md` |
| Teaching a developer how a subsystem works (5-layer lesson templates & interactive probes) | `skills/teaching_and_onboarding.md` |
| Spinning up local environment, running the self-healing test loop (`SELF_HEALING_TEST_LOG.md`), and generating `00_HUMAN_VERIFICATION_GUIDE.md` | `skills/autonomous_validation_and_artifacts.md` |
| Optimizing slow SQL queries, background jobs, N+1 loops, or preventing lock escalation | `skills/performance_and_query_tuning.md` |
| Pausing/ending a multi-session task, compacting context, or handing over state (`HANDOFF_STATE.md`) between agents | `skills/session_handoff_and_resume.md` |
| Multi-session / multi-ticket project, epic decomposition, or working with a `Tickets/` board (**Agentic Kanban**) | `skills/kanban_workflow.md` |
| Producing SQL change scripts, data-fix scripts, schema migrations, or verifying against `SCHEMA_INDEX.md` | `skills/database_change_scripts.md` |
| Dry-running on a local/sandbox environment, muting non-prod comms, or capturing `./Verification/` evidence | `skills/local_testing.md` |
| Staging changes, branching, handling environment drift, or writing Release Manifests & Operational Runbooks | `skills/git_and_release_delivery.md` |
| Operating `./docs/wiki/`, updating `MASTER_MEMORIES.md`, writing Durable Deep Links, or anonymizing findings | `skills/knowledge_wiki_and_memories.md` |
| Probing REST/RPC/GraphQL contracts, triaging defects vs rulings questions (`RQ-NNN`), or using Agent Tool Taxonomies | `skills/api_and_contract_testing.md` |
| Auditing ISO/SOC2 security controls, GDPR/tenant isolation, secret placeholders, or pre-migration checks | `skills/security_privacy_and_compliance.md` |
| Bootstrapping a new repository with `docs/`, `tasks/`, `Tickets/`, or using standard file templates | `skills/project_kickoff.md` |
