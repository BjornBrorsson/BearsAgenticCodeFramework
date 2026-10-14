# Skill: Naming & Test Conventions (Startup / Solo Edition)

> **Load When:** Naming files, scripts, DB objects, or writing automated tests.

---

## 1. Naming Philosophy: Follow the Repo

* **No Mandatory Ticket Prefixes:** Unlike the Enterprise Edition, you do not need `[PROJECT_PREFIX]_[TASK_ID]_` prefixes unless the repository already uses them. Match whatever style (`kebab-case`, `camelCase`, `PascalCase`, `snake_case`) the codebase uses.
* **Sensible Greenfield Defaults (When Repo Has None):**

| Artifact | Default Pattern | Example |
|---|---|---|
| **Feature Scratch Folder** | `./tasks/<feature-slug>/` | `./tasks/stripe-webhook-retry/` |
| **Kanban Ticket** | `Tickets/<Column>/ticket-YYYY-MM-DD-<slug>.md` or `<EPIC>-<NNN>_<slug>.md` | `Tickets/Ongoing/PAY-002_webhook-retry.md` |
| **SQL Fix / Migration Script** | `YYYYMMDD_<short_desc>.sql` | `20261013_backfill_subscription_status.sql` |
| **Database Views / Triggers** | `v_<name>` / `tr_<name>` | `v_active_subscriptions`, `tr_audit_payments` |
| **Feature Wiki Page** | `./docs/wiki/<feature_snake_or_kebab>.md` | `./docs/wiki/billing_webhooks.md` |

---

## 2. Automated Test Standards

* **No Fake Tests:** Never write `assert(true)`. Every test must run real code and assert concrete output.
* **Reuse Existing Setup:** Use the repository's existing test runner, fixtures, and seed helpers.
* **Minimum 4-Case Coverage:**
  1. **Happy Path** (standard valid input)
  2. **Null / Empty / 0-Row Input** (returns empty cleanly without crashing)
  3. **Edge / Boundary Input** (zero/negative numbers, max lengths, UTF-8 characters `å, ä, ö, €`)
  4. **Error / Unauthorized Path** (throws/logs expected error cleanly)
