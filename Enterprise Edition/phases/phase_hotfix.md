# Phase 4: HOTFIX — Urgent / Production Incident Overlay (Enterprise Edition)

> **Gatekeeper:** Read this file **IN ADDITION TO** `phase_research.md`, `phase_build.md`, and `phase_finalize.md` during an urgent production incident or P0/P1 outage.
> **Core Rule:** This overlay **compresses ceremony — it NEVER bypasses safety (§1), verification, or rollback planning.**

---

## 1. Immediate Triage & Blast-Radius Statement

In your first response, state:
1. **Affected Surface:** Component/client, environment, service/job/endpoint, and DB tables involved.
2. **Blast Radius:** What data, financial balances, or downstream integrations are at risk if the failure continues vs. if the fix misfires.
3. **Mitigation vs. Root-Cause Fix:** Whether an immediate feature-flag toggle, job pause, or rollback can stabilize production while the root cause is fixed.

## 2. What MAY Be Compressed
* **ISOLATE:** Reuse an existing `./tasks/<task>/` folder for the affected component, or scaffold with `#HOTFIX-YYYYMMDD` and backfill the ticket ID before handover.
* **ORIENT:** Go straight to the affected table in `SCHEMA_INDEX.md`, the relevant section of `MASTER_MEMORIES.md`, and the failing handler — **but still verify the schema and memories before coding**.
* **Specs File:** Start with a concise 5-line Incident Brief (Symptom, Root Cause, Scope, Fix, Rollback) and complete it during `phase_finalize.md`.

## 3. What Must NEVER Be Compressed
* **Safety Guardrails (§1):** `SELECT`-only + non-locking reads on shared/prod DBs; deliver DML/DDL as reviewable scripts; never modify read-only core without permission; keep non-prod comms muted.
* **Smallest Reversible Change:** Fix only the broken invariant — zero opportunistic refactoring. Keep `try-catch`, structured logging, and pre-flight checks intact.
* **Pre/Post Checks & Verification:** Every hotfix SQL script must include pre-check `SELECT` counts, `BEGIN TRANSACTION`, row-count guards, post-check `SELECT` verification, and an explicit **Rollback Script**. Save verification proof in `./Verification/`.
* **Post-Mortem (`RECORD`):** Log the incident timeline (`Detected → Diagnosed → Fixed → Verified`) and a Five-Why post-mortem + prevention item in `PROJECT_LOG.md`.
