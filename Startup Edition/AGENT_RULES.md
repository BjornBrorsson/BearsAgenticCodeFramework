# Agentic Workspace Rules — Core, Guardrails & Phase Router (Startup / Solo Edition)

> **Architecture (Progressive Disclosure — Lightweight):** This file is the **always-loaded core** for solo developers, startups, and side projects. It keeps ceremony low while enforcing hard safety guardrails (§1) and a **self-improving knowledge base** via on-demand modules in `./phases/` and `./skills/` (§2).
>
> **Edition Policy:** Agents may edit `./src/` directly, no enterprise ticket IDs or filename prefixes are required, and **Agentic Kanban (`Tickets/`)** is the recommended default task tracker once a project grows beyond a single task.
> **Companions:** `workspace_architecture_blueprint_SE.md` and `agentic_rules_blueprint_SE.md`.

---

## 0. Core Principles & Path Conventions

* **Core Principle:** Ship quickly without breaking production, leaking secrets, or solving the same problem twice.
* **Precedence (on conflict):** `Safety (§1)` → `explicit user instructions` → `this file & loaded phase/skill files` → `agent defaults`. Everything except §1 is an overridable default; if you override one, say so and why.
* **Less Code is Better Than More Code:** Reuse existing helpers and patterns before writing new ones. Keep code compact, comment the *why* (intent and invariants), and follow the repo's existing style.
* **Sub-Agents Encouraged Where Appropriate:** When your agent runtime supports sub-agents, use them to offload broad repo searches, parallel V1 ticket specification, or independent non-overlapping tasks so the main context stays clean. Always pass §1 safety rules in sub-agent prompts and never let two parallel sub-agents edit the same file (see `skills/subagent_orchestration.md`).
* **Path Convention:** Paths are relative to the repo root (`./docs/`, `./src/`, `./tasks/`, `./Tickets/`).

---

## Core Loop (Every Session)

> **ORIENT → ISOLATE → BUILD → VERIFY → RECORD**

| Loop Step | Core Contract | Mandatory Phase File | Recommended Skills (Load When Applicable) |
|---|---|---|---|
| **1. ORIENT** | Read `./docs/DOCUMENTATION_INDEX.md`, the relevant `./docs/wiki/<feature>.md` page, and `./docs/MASTER_MEMORIES.md` (plus `./docs/schemas/SCHEMA_INDEX.md` if touching SQL/models) before searching code. | `phases/phase_research.md` *(or `phase_investigate.md` / `phase_teach.md`)* | `knowledge_wiki_and_memories.md`, `subagent_orchestration.md` |
| **2. ISOLATE** | Keep scratch work, logs, and verification outputs in `./tasks/<feature>/` (or claim a card in `Tickets/Ongoing/`). | `phases/phase_research.md` *(or `phase_autonomous_backlog.md` / `phase_plan_and_design.md`)* | `kanban_workflow.md`, `project_kickoff.md`, `architecture_and_premortem.md`, `requirements_and_spec_writing.md` |
| **3. BUILD** | Edit `./src/` directly; reuse patterns; comment the *why*; wrap external I/O, DB, and API calls in `try-catch` and real logging; run pre-flight checks. | `phases/phase_build.md` *(or `phase_refactor.md`)* | `conventions_and_naming.md`, `database_change_scripts.md`, `performance_and_query_tuning.md` |
| **4. VERIFY** | Run it for real. No fake tests (`assert(true)`). Clear build/lint errors; wrap local DB mutation tests in `BEGIN TRANSACTION … ROLLBACK`; save manual review proof in `./Verification/`. | `phases/phase_test_and_validate.md` & `phases/phase_finalize.md` *(or `phase_review.md`)* | `autonomous_validation_and_artifacts.md`, `local_testing.md`, `api_and_contract_testing.md` |
| **5. RECORD** | Update `./docs/wiki/<feature>.md` (with verified Durable Deep Links) and `./tasks/<feature>/PROJECT_LOG.md`; log any twice-hit or >30-min quirk in `./docs/MASTER_MEMORIES.md`. | `phases/phase_finalize.md` | `knowledge_wiki_and_memories.md`, `git_and_release_delivery.md`, `session_handoff_and_resume.md` |

---

## 1. Safety & Data Guardrails (HARD RULES)

* **Zero Hardcoded Secrets:** API keys, tokens, passwords, and private keys go in `.env` or environment variables — **never** in code, logs, or git. Use `<YOUR_API_KEY>` placeholders in templates/docs.
* **Mute Outbound Comms in Dev/Test:** Always set email (`smtp`), SMS, push notifications, webhooks, and payment gateways (Stripe, Swish, etc.) to test mode or disabled (`0` / `false`) in local/dev configs so test runs never reach real users.
* **Database Safety:**
  * Check `./docs/schemas/SCHEMA_INDEX.md` or the actual models/migrations before writing SQL — never guess table or column names.
  * **Never** run destructive DML/DDL (`DROP`, `TRUNCATE`, `DELETE`, `UPDATE`, `ALTER`) directly against a shared or production database. Deliver change scripts for shared/prod DBs as reviewable `.sql` files: *"Here is the script. Please review it carefully and execute it yourself."*
  * On a local dev database you may run migrations and queries directly — wrap non-persisted test mutations in `BEGIN TRANSACTION … ROLLBACK TRANSACTION`.
* **Stay Reversible & Protect Files:** Commit or stash before large refactors. Never delete existing user files without explicit consent; archive superseded verification outputs into `./Verification/archive/`.
* **Proceed vs. Ask:** Proceed by default and note assumptions in `PROJECT_LOG.md`. **STOP and ask** before irreversible actions (production data changes, unrecoverable deletes, git history rewrites, real payments/messages) or high lock-in choices (new core framework dependency, breaking schema/API change).

---

## 2. Phase Router & Gatekeeper (MANDATORY)

### 2.1 The Gatekeeper Rule

1. **Mandatory On-Demand Read:** Do **not** enter a phase or declare a task complete without first reading the mapped `phases/phase_*.md` file in full.
2. **Re-Read After Compaction:** Re-read the active phase file if session context was compacted.
3. **Phase Signposting:** Briefly note phase transitions (`[Phase: RESEARCH]`, `[Phase: BUILD]`, `[Phase: FINALIZE]`).

### 2.2 Phase Modules (`./phases/`)

| Development Phase | Loop Steps Covered | Trigger — Read This File Before… | Module Path |
|---|---|---|---|
| **Research & Isolation** | `ORIENT`, `ISOLATE`, `GROUND` | Starting a development task or setting up `./tasks/<feature>/` / `Tickets/` | `phases/phase_research.md` |
| **Plan & Architecture Design** | `ORIENT` → `GROUND` → `PRE-MORTEM` | Asking clarifying questions, designing architecture, catching pitfalls before coding, and getting go-ahead | `phases/phase_plan_and_design.md` |
| **Investigate & Explain** | `ORIENT` → `TRACE` → `RECORD` | Answering *"How does X work?"* or *"Why does Y behave like this?"* (no `./tasks/` setup needed) | `phases/phase_investigate.md` |
| **Teach & Onboard** | `MODEL` → `FLOW` → `WALKTHROUGH` | Teaching how a feature/subsystem works with diagrams, guided code tours, and exercises | `phases/phase_teach.md` |
| **Build & Robustness** | `BUILD` | Writing or modifying code in `./src/`, SQL scripts, or configs | `phases/phase_build.md` |
| **Refactor (Golden Master)** | `BASELINE` → `SIMPLIFY` → `DIFF` | Cleaning up or simplifying code while proving `Before == After` output parity | `phases/phase_refactor.md` |
| **Test, Validate & Self-Heal** | `SPIN-UP` → `TEST` → `SELF-HEAL` | Spinning up local servers, self-healing test failures, and creating `./Verification/` human review proof | `phases/phase_test_and_validate.md` |
| **Finalize, Verify & Record** | `VERIFY`, `RECORD` | Archiving `./Verification/` artifacts, updating wiki/logs/memories, or reporting "Done" | `phases/phase_finalize.md` |
| **Debug & Root-Cause** | `ORIENT` → `VERIFY` (Diagnostic) | Debugging a bug, tracing logs/state, or reproducing a failure | `phases/phase_debug.md` |
| **Code Review & Sign-Off** | `AUDIT` → `SIGN-OFF` | Reviewing PRs, auditing security/performance, and issuing a formal Sign-Off Verdict | `phases/phase_review.md` |
| **Autonomous Backlog / V1 Spec** | `GAP-SPEC` or `QUEUE-DRAIN` | Unattended batch runs: *"Speca allt som är kvar för V1"* (Mode A) or *"Slutför så många tickets du kan"* (Mode B) | `phases/phase_autonomous_backlog.md` |
| **Hotfix / Urgent Overlay** | All Steps (Compressed) | Fixing an urgent production bug — read **in addition to** `phase_research`, `phase_build`, and `phase_finalize` | `phases/phase_hotfix.md` |

### 2.3 Skill Modules (`./skills/`)

| Task / Domain Trigger | Skill File to Read |
|---|---|
| Naming files, scripts, DB objects, or writing automated tests | `skills/conventions_and_naming.md` |
| Spawning sub-agents for parallel repo exploration, audits, or disjoint tasks | `skills/subagent_orchestration.md` |
| Planning architecture, asking critical clarifying questions, or running a Pre-Mortem | `skills/architecture_and_premortem.md` |
| Turning rough ideas/notes into testable specs & Kanban cards | `skills/requirements_and_spec_writing.md` |
| Teaching how a feature/library works (5-layer lesson template) | `skills/teaching_and_onboarding.md` |
| Spinning up local servers, running the self-healing test loop (`SELF_HEALING_TEST_LOG.md`), and generating `00_HUMAN_VERIFICATION_GUIDE.md` | `skills/autonomous_validation_and_artifacts.md` |
| Optimizing slow SQL queries, N+1 loops, API routes, or jobs | `skills/performance_and_query_tuning.md` |
| Pausing/ending an unfinished task or handing over state (`HANDOFF_STATE.md`) between sessions/agents | `skills/session_handoff_and_resume.md` |
| Multi-session project, roadmap/epic decomposition, or using a `Tickets/` board (**Agentic Kanban — recommended default tracker**) | `skills/kanban_workflow.md` |
| Writing SQL migrations, data-fix scripts, or verifying schemas | `skills/database_change_scripts.md` |
| Running local dry-runs, muting dev comms, handling UTF-8 payloads, or saving `./Verification/` proof | `skills/local_testing.md` |
| Branching, release checklists, or writing a lightweight Runbook | `skills/git_and_release_delivery.md` |
| Updating `./docs/wiki/<feature>.md`, `./docs/MASTER_MEMORIES.md`, or writing Durable Deep Links | `skills/knowledge_wiki_and_memories.md` |
| Testing REST/RPC/GraphQL endpoints, triaging bugs vs product questions (`RQ-NNN`), or using Agent Tool Taxonomies | `skills/api_and_contract_testing.md` |
| Checking `.env` secret safety, GDPR/user data privacy, or pre-migration checks | `skills/security_privacy_and_compliance.md` |
| Bootstrapping `docs/`, `tasks/`, and `Tickets/` into a new side project or repo | `skills/project_kickoff.md` |
