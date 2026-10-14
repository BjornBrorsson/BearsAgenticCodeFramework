# Skill: Security, Data Privacy, Tenant Isolation & Compliance (Enterprise Edition)

> **Load When:** Auditing code or configs for security, working with multi-tenant/client customizations, handling secrets/certificates, anonymizing documentation, or checking pre-migration safety.
> **Precedence:** Safety & Data Privacy (`AGENT_RULES.md` §1) has the **highest precedence** and cannot be overridden.

---

## 1. Information Security Controls (ISO/IEC 27001 / SOC 2 Alignment)

1. **Least Privilege & Read-Only Default:**
   * Treat all environments as production-like except explicitly designated local disposable sandboxes.
   * Execute **SELECT-only, non-locking** queries on databases; deliver DML/DDL as reviewable scripts for human execution.
2. **Zero Hardcoded Secrets:**
   * **Never** commit, log, or hardcode API keys, passwords, JWT secrets, private keys, or connection strings.
   * Use environment variables, secret managers, integrated authentication, or explicit placeholders (`<YOUR_API_KEY>`, `<CLIENT_SECRET>`).
3. **Full Traceability:**
   * Every change maps to a `[TASK_ID]`, a spec (`[TASK_ID]_Specs.md`), a `PROJECT_LOG.md` entry, and reviewable evidence in `./Verification/`.

---

## 2. Strict Client / Tenant Isolation & GDPR Anonymization

* **Cross-Client Isolation:** Never copy or expose client-specific data, business logic, credentials, or PII from one client/tenant module into another.
* **Mandatory Anonymization in Shared Knowledge (`./docs/wiki/`, `./docs/MASTER_MEMORIES.md`):**
  * Replace client codes/names with `"Client A"` or `"Tenant 001"`.
  * Replace real national IDs, account numbers, emails, and hostnames with synthetic placeholders.

---

## 3. Non-Production Outbound Communication Lockdown

* In all non-production configs, explicitly disable or mock outbound SMTP email, SMS, push notifications, external webhooks, and live payment gateways (`0` / `false` / sandbox URL) so test runs can never reach real users.

---

## 4. Destructive Operation & File Guardrails

* **Never Delete or Overwrite Files Without Explicit Per-Case Consent:** Draft to new timestamped files or inside `./tasks/<task>/`, and move superseded verification files to `./Verification/archive/`.
* **Read-Only Core:** Never modify `[CORE_LIBRARY_PATH]` / `./src/core/` without explicit permission.
