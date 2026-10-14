# The Self-Improving Agentic Workspace — Architectural Blueprint (Startup Edition)

How to set up a Self-Improving Agentic Workspace for a small or solo-dev repository: low overhead, fast iteration, and a persistent knowledge base so problems are not re-solved. Product-agnostic — drop it into any repo, usable by any AI agent (Cursor, Copilot, Devin, Windsurf).

> **Modular Progressive Disclosure Architecture:** This file explains how the workspace is built. Its runtime companion is **`AGENT_RULES.md`** (lightweight always-loaded core + safety rules + Phase/Skill Router) paired with on-demand modules in **`./phases/`** (12 phase modules) and **`./skills/`** (16 skill modules). The legacy single-file reference remains in `agentic_rules_blueprint_SE.md`. Keep them in sync.

---

## 1. Purpose

Treating an AI agent as a stateless chat box has two costs:
1. **Lost knowledge.** A quirk solved in one session is forgotten; the next session re-debugs it.
2. **Search overhead.** With no map, the agent greps the whole repo, wasting tokens and context.

The fix is a two-loop structure — a per-task scratchpad plus a durable repository knowledge base:

```
        +-----------------------------------------------------------+
        |               GLOBAL KNOWLEDGE  (Loop 2)                  |
        |  docs/MASTER_MEMORIES.md      — reusable bug/quirk fixes    |
        |  docs/wiki/*.md               — feature logic + code links |
        |  docs/DOCUMENTATION_INDEX.md  — where things live          |
        +----------------------------^------------------------------+
                       promote a fix when any feature could hit it
                                     |
        +----------------------------+------------------------------+
        |               LOCAL SCRATCHPAD  (Loop 1)                  |
        |  tasks/<feature>/ — drafts, scratch scripts, short logs    |
        +----------------------------^------------------------------+
                                     | execute
                             [ AI Agent / LLM Tooling ]
```

* **Loop 1 (local):** `./tasks/<feature>/` holds drafts, throwaway scripts, and a short log. The repo and git stay clean.
* **Loop 2 (global):** reusable fixes go in `MASTER_MEMORIES.md`; feature documentation goes in `docs/wiki/` with durable links into the code.

---

## 2. Directory Layout

```
[Repo Root]/
├── AGENT_RULES.md / AGENTS.md     <-- Always-loaded core rules, safety (§1) & Phase/Skill Router (§2)
├── phases/                        <-- On-demand phase modules (research, plan_and_design, investigate, teach, build, refactor, test_and_validate, finalize, debug, review, autonomous_backlog, hotfix)
├── skills/                        <-- On-demand skill modules (kanban_workflow, subagent_orchestration, autonomous_validation_and_artifacts, database_change_scripts, etc.)
├── Tickets/                       <-- Recommended default tracker: Agentic Kanban board (Backlog/Ready, Ongoing, Assistance Required, Blocked, Completed)
├── docs/
│   ├── DOCUMENTATION_INDEX.md     <-- Map of where things live; avoids blind grep
│   ├── MASTER_MEMORIES.md         <-- Reusable bug/quirk registry
│   └── wiki/
│       ├── system_overview.md     <-- One-screen architecture map
│       └── <feature>.md           <-- Feature logic + durable code links
├── src/                           <-- Production code (edited directly)
├── scripts/ (or tools/)           <-- Seed/run/test helpers
└── tasks/
    └── <feature>/                 <-- Per-feature scratchpad
        ├── PROJECT_LOG.md         <-- Changes + one-line post-mortem + rollback
        ├── PROJECT_MEMORIES.md    <-- Notes specific to this feature
        └── Verification/          <-- Human verification guide, logs, proof (+ archive/)
```

> Optional: add `docs/schemas/SCHEMA_INDEX.md` once there is a database worth mapping.

---

## 3. Knowledge Routing

Send each note to a single home so each file stays high-signal.

| Information | Home |
|---|---|
| What you changed + any bug fixed | `tasks/<feature>/PROJECT_LOG.md` |
| A quirk specific to this feature | `tasks/<feature>/PROJECT_MEMORIES.md` |
| How a feature works + where its code is | `docs/wiki/<feature>.md` |
| A bug/quirk any feature could hit | `docs/MASTER_MEMORIES.md` |
| A new doc/place worth knowing | `docs/DOCUMENTATION_INDEX.md` |

Promote a note from local to global when any future feature could hit it.

---

## 4. Durable Deep Links

A wiki with stale line numbers misleads agents. Line numbers drift on every edit, so:

1. **Anchor on the symbol** — a function/class/route name — not a bare line range.
2. **Line is a hint only** — write `~L42`.
3. **Date-stamp** each link with `_Verified: YYYY-MM-DD_`.
4. **Verify on read** — confirm the symbol still exists near the hint before relying on it.
5. **Repair on touch** — when you edit a linked file, update its link and verified date in the same change.

```
**Webhook handler** — `handleStripeWebhook()` in [src/routes/billing.ts](../../src/routes/billing.ts) (~L10). _Verified: YYYY-MM-DD._
```

> Path note: from `docs/wiki/billing.md`, the repo root is `../../`. On GitHub-hosted repos, a commit-pinned permalink is more durable still.

**Granularity & freshness:** one page per *feature* (not per file), with a status line at the top:
```
> Status: Current | Last verified: YYYY-MM-DD
```

---

## 5. File Templates

### `docs/DOCUMENTATION_INDEX.md`
```markdown
# Documentation Index
Use this to open the right folder directly instead of searching the whole repo.

## Folder Map
* Core logic: `./src/`
* Config: `./src/config/` (or `./config/`)
* Scratchpad: `./tasks/`

## Feature Wiki
* Auth & login: [auth](./wiki/auth.md)
* Payments (Stripe): [billing](./wiki/billing.md)
```

### `docs/wiki/<feature>.md`
```markdown
# Feature: <Name>
> Status: Current | Last verified: YYYY-MM-DD

## What it does
2–3 sentences: the behavior and any key rules.

## Where the code is (durable links)
* **<role>** — `symbolName()` in [path](../../src/...) (~L10). _Verified: YYYY-MM-DD._
* **<role>** — `ClassName` in [path](../../src/...) (~L45). _Verified: YYYY-MM-DD._

## Keys & config
* `ENV_KEY` — what it controls (set in `.env`).

## Gotchas
One-liners; link to MASTER_MEMORIES for larger ones.

---
## Update Log
* YYYY-MM-DD (agent): what changed / links re-verified.
```

### `docs/MASTER_MEMORIES.md`
```markdown
# Master Memories
> Reusable bugs, quirks, and workarounds. Read before debugging or adding an integration.

## Entry template (Two-Strike Rule)
### <short title>
* **Verified:** YYYY-MM-DD
* **Context:** when it happens
* **Symptom:** what you see
* **Fix:** the durable workaround
* **Example:** minimal snippet

> Add it the second time you hit it — or the first if it cost more than ~30 minutes.
```

### `tasks/<feature>/PROJECT_LOG.md`
```markdown
# Log: <feature>
## Changes — YYYY-MM-DD
* Refactored `user.ts` to allow email-less signup.
* Added Stripe webhook route in `billing.ts`.

## Post-Mortem
* **Cause:** JSON parser failed on a null webhook body.
* **Prevention:** added an early-exit guard before parse.
```

---

## 6. Bootstrapping (first-time setup)

1. **Create dirs:** `docs/`, `docs/wiki/`, `tasks/`.
2. **Build the index:** scan the repo, write `DOCUMENTATION_INDEX.md` with the real folder map.
3. **Sketch the system:** write `wiki/system_overview.md` (one screen, real components).
4. **Open the memory file:** create `MASTER_MEMORIES.md` from the template (empty is fine).
5. **Install the rules:** copy `agentic_rules_blueprint.md` to `.cursorrules` / `.windsurfrules`.
6. **Grow with use:** each task that touches a feature leaves behind its wiki page — no upfront documentation effort.

---

## 7. Runtime SOP (the loop, every task)

```
[ORIENT]            -> [ISOLATE]        -> [BUILD]              -> [VERIFY]        -> [RECORD]
- index + wiki         - make tasks/      - edit src/ directly     - run it            - update wiki + links
- check memories         <feature>/       - reuse + comment why    - no fake tests     - log changes
- skip blind grep      - draft there      - guard volatile I/O     - clear lint        - record twice-hit quirks
```

* **Orient** — index → matching wiki page (follow its links) → `MASTER_MEMORIES.md`. Do not repo-wide grep first.
* **Isolate** — ask for a feature name; create `./tasks/<feature>/`; keep scratch work there.
* **Build** — edit core directly; reuse existing patterns; comment intent; match repo conventions; guard volatile I/O.
* **Verify** — run it; real (not fake) tests; clear build/lint; transaction-wrap any local DB test.
* **Record** — refresh `docs/wiki/<feature>.md` with verified, date-stamped links; update the log; promote any twice-hit quirk to `MASTER_MEMORIES.md`; tell the user what changed.

---

## 8. Drop-in Rules Stub

Save at the repo root as `.cursorrules` / `.windsurfrules`:

```markdown
# Self-Improving Workspace — Startup. Full rules: ./docs/agentic_rules_blueprint.md
Loop every task: ORIENT -> ISOLATE -> BUILD -> VERIFY -> RECORD.
1. Orient: read docs/DOCUMENTATION_INDEX.md, the matching docs/wiki/ page, and docs/MASTER_MEMORIES.md before searching code. If missing, create it.
2. Isolate: ask for a feature name; create ./tasks/<feature>/ and keep scratch work there.
3. Build: edit/refactor src/ directly. Reuse first, comment the "why," match existing style.
4. Safety (hard rules): secrets in .env; mute outbound comms (mail/SMS/payments) in dev; check the schema before SQL; commit/stash before large refactors.
5. Verify: run it; no assert(true) tests; clear build/lint before "done."
6. Record: update docs/wiki/ with durable links (anchor on symbol name + ~line + verified date; repair on touch); update the log; record any twice-hit quirk in docs/MASTER_MEMORIES.md.
```
