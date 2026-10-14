# Skill: Database Change Scripts, Schema Lookup & Transaction Safety (Enterprise Edition)

> **Load When:** Writing any SQL query, data-fix script, migration, or schema change.
> **Hard Guardrails (Core §1.2):**
> 1. **NEVER** execute `UPDATE`, `INSERT`, `DELETE`, `MERGE`, `ALTER`, `TRUNCATE`, `DROP`, or other DML/DDL directly on any shared, staging, or production database.
> 2. Direct AI diagnostic queries **MUST** use non-locking reads (`WITH (NOLOCK)` / `READ UNCOMMITTED` or engine equivalent).
> 3. Deliver all change scripts for non-local environments as reviewable `.sql` files in `./tasks/<task>/` with the note:
>    *"Here is the script. Please review it carefully and execute it yourself."*

---

## 1. Schema Verification Protocol (Mandatory Before Writing SQL)

Never guess a table name, column name, datatype, or constraint.
1. Consult `./docs/schemas/SCHEMA_INDEX.md` to locate the authoritative schema or model definition file.
2. Open the schema/migration file and verify:
   * Exact table & column names
   * Datatypes, lengths, and numeric precision
   * Nullability (`NOT NULL` vs `NULL`) and default values
   * Primary keys, unique constraints, foreign keys, and triggers
3. If the repository uses an ORM/migration tool (Flyway, Liquibase, Entity Framework, Prisma, Alembic), follow its migration file convention alongside this safety standard.

---

## 2. Production-Grade SQL Change Script Template

Save drafted scripts in `./tasks/<task>/[PREFIX]_[TASK_ID]_Script_[Desc].sql`:

```sql
/* ============================================================================
   File        : [PREFIX]_[TASK_ID]_Script_[Description].sql
   Component   : <Component / Client>
   Ticket ID   : #<TASK_ID>
   Author      : <Author / Agent draft — REVIEW BEFORE EXECUTION>
   Created     : YYYY-MM-DD
   Target Env  : [local-dev | staging | prod]
   Purpose     : <What this script changes and why>
   Dependencies: <Prerequisite migrations/scripts, or None>
   Rollback    : <See companion rollback block at bottom of file>
   ============================================================================ */

SET NOCOUNT ON;
SET XACT_ABORT ON; -- Abort and roll back entire batch on any runtime error

BEGIN TRY
    BEGIN TRANSACTION;

    PRINT '[YYYY-MM-DD HH:MM:SS] [INFO] [Script] - Starting pre-flight checks...';

    /* 1. PRE-FLIGHT & IDEMPOTENCY CHECKS */
    IF OBJECT_ID('dbo.target_table', 'U') IS NULL
        RAISERROR('Pre-flight failed: dbo.target_table does not exist.', 16, 1);

    /* 2. BEFORE-STATE DIAGNOSTIC SNAPSHOT */
    SELECT COUNT(*) AS AffectedRowCount_Before
    FROM dbo.target_table
    WHERE /* exact predicate */;

    /* 3. APPLY CHANGE (Smallest Possible Scope) */
    UPDATE dbo.target_table
    SET    /* columns */
    WHERE  /* exact predicate */;

    DECLARE @UpdatedRows INT = @@ROWCOUNT;
    PRINT CONCAT('[INFO] Rows updated: ', @UpdatedRows);

    IF @UpdatedRows > 1000 -- adjust expected safety ceiling
        RAISERROR('Safety ceiling exceeded (%d rows). Rolling back.', 16, 1, @UpdatedRows);

    /* 4. AFTER-STATE VERIFICATION */
    SELECT COUNT(*) AS AffectedRowCount_After
    FROM dbo.target_table
    WHERE /* post-condition predicate */;

    /* SAFETY GATE: Default to ROLLBACK for dry-run verification.
       Uncomment COMMIT TRANSACTION only after verifying Before/After counts. */
    -- COMMIT TRANSACTION;
    ROLLBACK TRANSACTION;
    PRINT '[INFO] Dry-run verified and rolled back cleanly.';

END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;
    THROW;
END CATCH;
GO

/* ============================================================================
   COMPANION ROLLBACK SCRIPT (Run if reverting after COMMIT)
   ============================================================================
   BEGIN TRANSACTION;
     -- Exact inverse UPDATE / DELETE / DROP IF EXISTS statements
   -- COMMIT TRANSACTION;
   ROLLBACK TRANSACTION;
   ============================================================================ */
```

---

## 3. Idempotent DDL & Pre-Restore Checks

* Always guard DDL statements so re-running a script cannot fail destructively:
  * Tables: `IF OBJECT_ID('dbo.MyTable', 'U') IS NULL CREATE TABLE ...`
  * Columns: `IF COL_LENGTH('dbo.MyTable', 'MyCol') IS NULL ALTER TABLE ...`
  * Views/Procs/Triggers: `CREATE OR ALTER VIEW / PROCEDURE / TRIGGER ...`
* Before database restores or cross-server migrations, check collation/charset/version compatibility (`SELECT SERVERPROPERTY('Collation')`) and warn on mismatch.
