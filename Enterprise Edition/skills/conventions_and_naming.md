# Skill: Naming & Artifact Conventions + Test Standards (Enterprise Edition)

> **Load When:** Naming files, scripts, database objects, parameters, service accounts, or authoring automated tests.
> **Precedence:** Repository-native conventions always win. Use the table below as the standard default when adding new enterprise task artifacts or working in greenfield modules.

---

## 1. Enterprise Artifact Naming Table

All task-scoped and custom artifacts follow the pattern `[PROJECT_PREFIX]_[TASK_ID]_...`:

| Artifact Type | Pattern / Convention | Example | Notes |
|---|---|---|---|
| **Task Workspace Folder** | `./tasks/[COMPONENT] - [TASK_ID] - [TASK_NAME]/` | `./tasks/Billing - #12345 - InvoiceRetryQueue/` | Isolates all specs, scratch scripts, logs, and verification outputs. |
| **Specification File** | `[TASK_ID]_Specs.md` or `[PREFIX]_[TASK_ID]_Specs.md` | `PAY_12345_Specs.md` | Single source of truth inside the task folder. |
| **General Files** | `[PREFIX]_[TASK_ID]_[desc].[ext]` | `PAY_12345_ArchitectureDiagram.md` | Clear, descriptive suffix. |
| **SQL Scripts** | `[PREFIX]_[TASK_ID]_Script_[desc].sql` | `PAY_12345_Script_BackfillInvoices.sql` | Deliver as reviewable text/file; never auto-run on shared/prod DBs. |
| **Automation / Shell Scripts** | `[PREFIX]_[TASK_ID]_Script_[desc].[ps1\|sh\|py]` | `PAY_12345_Script_ImportBatch.ps1` | Must include structured logging & `try-catch`. |
| **Report Queries / Layouts** | `[PREFIX]_q_[ReportID]_[name]` / `[PREFIX]_d_[ReportID]_[name]-[lang]` | `PAY_q_Re03_AgedLedger.sql` | Separate data query (`q_`) from localized layout (`d_...-<lang>`). |
| **Integration Exports** | `[PREFIX]_[IntID]_export_[desc]_[YYYYMMDDHHMMSS].[ext]` | `PAY_Int02_export_payments_20261013120000.xml` | Timestamped runtime output files. |
| **Database Tables** | `[PREFIX]_[TASK_ID]_[name]` | `PAY_12345_auth_req` | No spaces; match repo casing (`snake_case` or `PascalCase`). |
| **Database Views** | `[PREFIX]_[TASK_ID]_v_[name]` | `PAY_12345_v_AgedDebts` | Always include `_v_` infix. |
| **Database Triggers** | `[PREFIX]_[TASK_ID]_tr_[name]` | `PAY_12345_tr_AuditInvoice` | Always include `_tr_` infix. |
| **Config Parameters / Flags** | `[PREFIX]__[name]` (**double underscore**) | `PAY__AllowBypass` | Double underscore namespaces custom parameters cleanly. |
| **Internal / Client Users** | `[org]_[first2][last2]` / `[CLIENT]_[first2][last2]` | `acme_bjbr` / `CLIA_bjbr` | Traceable service/test user accounts. |
| **Multi-Region / Country** | Insert ISO country/region code (`SE`, `NO`, `FI`, `DE`, `US`) | `PAY_SE_12345_TaxExport` | Required when a system operates multi-country ledgers/tenants. |

---

## 2. Automated Test Standards

* **Location:** Place tests in the repository's standard test directory (`tests/`, `__tests__/`, `spec/`) and mirror the source tree structure.
* **No Fake Tests:** Never write `assert(true)` or placeholder assertions. Every test must invoke the real unit/service/query and assert concrete state or return values.
* **Reuse Shared Fixtures:** Reuse existing test helpers, factories, and generic seed routines before creating new test scaffolding.
* **Mandatory 4-Point Coverage Matrix:**
  1. **Happy Path:** Valid inputs produce the exact expected state/output.
  2. **Null / Empty / 0-Row Inputs:** Empty datasets, `null`/`undefined` optional fields, and 0-byte files are handled gracefully (logging `[WARNING]` where appropriate).
  3. **Boundaries & Oversized Inputs:** Max string lengths, large batch sizes, zero/negative financial amounts, special UTF-8 characters (`å, ä, ö, æ, ø, €`), and date boundaries.
  4. **Permission Blocks & Exception Logging:** Unauthorized roles/callers are rejected, and forced I/O or validation failures trigger structured `try-catch` and `[ERROR]` logs.
