# Skill: Performance, SQL Query Tuning & Job Optimization (Enterprise Edition)

> **Load When:** Optimizing slow SQL queries, reports, background jobs, or high-volume API endpoints.

---

## 1. Measure Before & After
* Capture baseline execution plans, I/O reads (`SET STATISTICS IO, TIME ON` / `EXPLAIN ANALYZE`), and step durations before changing code, and save the before/after comparison in `./tasks/<task>/Verification/performance_comparison.txt`.

## 2. Core Optimization Rules
* **Keep SQL Predicates SARGable:** Avoid wrapping indexed columns in functions (`CONVERT`, `CAST`, `ISNULL`, `YEAR()`) in `WHERE` or `JOIN` clauses so the optimizer uses Index Seeks instead of Scans.
* **Eliminate N+1 Queries in Loops:** Replace per-row queries inside loops with a single set-based join or batch prefetch upfront.
* **Filter on Invariant Indexed IDs:** Always filter and classify on indexed status/type IDs rather than unindexed localized description strings.
* **Batch Large Mutations:** Process large updates/deletes in chunks (e.g. `TOP (2000)` loop) to prevent table lock escalation and transaction log bloat.
