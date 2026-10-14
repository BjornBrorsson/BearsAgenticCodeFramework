# Skill: Database Change Scripts & Safe SQL (Startup Edition)

> **Load When:** Writing SQL queries, schema migrations, or data-fix scripts.

---

## 1. Local Dev DB vs. Shared / Production DB

* **Local Dev DB:** You may run migrations and queries directly. Wrap exploratory or test mutations in `BEGIN TRANSACTION … ROLLBACK TRANSACTION` so test runs don't pollute local state.
* **Shared / Staging / Production DB:** **Never** run `UPDATE`, `INSERT`, `DELETE`, `ALTER`, `TRUNCATE`, or `DROP` directly. Deliver a reviewable `.sql` script in `./tasks/<feature>/` with the note:
  > *"Here is the script. Please review it carefully and execute it yourself."*
* **Verify Schema First:** Always check `./docs/schemas/SCHEMA_INDEX.md`, ORM models, or migration files before writing SQL — never guess column names or types.
* **ORM / Migration Tool Priority:** If the repo uses Prisma, Drizzle, Flyway, EF, or Alembic, generate migrations through that tool rather than ad-hoc SQL.

---

## 2. Safe SQL Change Script Template (For Shared / Prod DBs)

```sql
/* ============================================================================
   File      : YYYYMMDD_<description>.sql
   Target Env: [dev | staging | prod]
   Purpose   : <What it changes and why>
   Rollback  : <See inverse statements at bottom>
   ============================================================================ */

BEGIN TRANSACTION;

  -- 1. Pre-check: inspect rows/counts that will be affected
  SELECT COUNT(*) AS BeforeCount FROM target_table WHERE /* predicate */;

  -- 2. Apply change (smallest scope)
  UPDATE target_table
  SET    /* columns */
  WHERE  /* predicate */;

  -- 3. Post-check: verify expected state
  SELECT COUNT(*) AS AfterCount FROM target_table WHERE /* post-predicate */;

-- Uncomment COMMIT only after verifying BeforeCount and AfterCount:
-- COMMIT TRANSACTION;
ROLLBACK TRANSACTION;

/* Companion Rollback (if reverting after COMMIT):
   UPDATE target_table SET ... WHERE ...; */
```
