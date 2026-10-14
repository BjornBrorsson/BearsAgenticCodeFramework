# Skill: Local & Sandbox Testing (Enterprise Edition)

> **Load When:** Dry-running changes in a local or disposable sandbox environment, configuring non-production settings, or capturing verification evidence in `./Verification/`.

---

## 1. Environment Permissions Matrix

| Environment | AI Permissions | Outbound Comms (Email/SMS/Webhooks/Payments) |
|---|---|---|
| **Local Disposable Sandbox** | Full execution & testing allowed (wrap non-persisted DB writes in `BEGIN TRAN … ROLLBACK`) | **MUST be disabled (`0`/`false`) or pointed to local mock** |
| **Task Workspace (`./tasks/<task>/`)** | Primary drafting & `./Verification/` storage | N/A |
| **Shared Dev / Staging / QA / Prod** | **Deliver-only** (`SELECT`-only non-locking reads; human deploys and executes scripts) | Muted in non-prod; controlled by ops in prod |

---

## 2. Mandatory Non-Production Hygiene

1. **Mute Outbound Communications:** Before running any job, service, or test locally, verify that SMTP mailers, SMS gateways, push notifications, external webhooks, and payment processors are set to `0` / `false` / mock endpoints.
2. **Explicit UTF-8 Encoding on HTTP/REST Probes:**
   Note that Windows PowerShell 5.x sends `Invoke-RestMethod -Body $json` as Windows-1252 by default, corrupting non-ASCII characters (`å, ä, ö, æ, ø, €`). Always send explicit UTF-8 bytes:
   ```powershell
   $bodyBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonPayload)
   Invoke-RestMethod -Uri $url -Method POST -Body $bodyBytes -ContentType "application/json; charset=utf-8" -Headers $headers
   ```
3. **Database Restore Sanity Check:** Verify SQL collation/charset compatibility before restoring `.bak` or dump files locally.

---

## 3. Dry-Run & Verification Evidence Workflow

1. **Archive Prior Verification Files:** Move any existing files in `./tasks/<task>/Verification/` into `./tasks/<task>/Verification/archive/` (never delete prior run evidence).
2. **Execute the 4 Test Cases:**
   * Happy path
   * Empty / 0-row / null input (confirm `[WARNING]` log and clean exit)
   * Boundary / oversized / UTF-8 input
   * Error / unauthorized input (confirm `try-catch` and `[ERROR]` log with stack trace)
3. **Save Proof to `./Verification/`:** Store log excerpts, test runner output, SQL before/after counts, and generated files in `./tasks/<task>/Verification/` and cite them in `PROJECT_LOG.md`.
