# Skill: Local Testing & Verification Evidence (Startup Edition)

> **Load When:** Running a feature locally, testing API endpoints/UI flows, or saving verification proof in `./Verification/`.

---

## 1. Pre-Run Safety Check
* Confirm `.env` / local config has outbound email, SMS, push notifications, webhooks, and payment gateways (Stripe, etc.) in **test/sandbox mode or disabled (`0`/`false`)**.

## 2. Run & Verify Checklist
1. **Archive Older Runs:** Move any previous verification files in `./tasks/<feature>/Verification/` into `./tasks/<feature>/Verification/archive/`.
2. **Build & Static Checks:** Run compile, typecheck, and linter (`npm run build`, `dotnet build`, `pytest`, etc.) and clear all errors.
3. **Exercise the Real Flow:** Hit the actual endpoint, UI view, or CLI command across:
   * Happy path
   * Empty / null / 0-row input
   * Boundary / special UTF-8 input (`å, ä, ö, €`) — *Note: in Windows PowerShell 5.x, encode JSON bodies with `[System.Text.Encoding]::UTF8.GetBytes($json)` so non-ASCII characters aren't corrupted.*
   * Error / unauthorized input
4. **Save Evidence:** Save test output, log excerpts (`[INFO]`/`[WARNING]`/`[ERROR]`), or before/after snapshots to `./tasks/<feature>/Verification/`.
