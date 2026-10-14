# Skill: Performance & Query Tuning (Startup Edition)

> **Load When:** Optimizing slow SQL queries, API routes, background jobs, or UI renders.

---

## Rules
1. **Measure First:** Capture before/after timings and query plans (`EXPLAIN ANALYZE` / `SET STATISTICS IO, TIME ON`) in `./tasks/<feature>/Verification/performance_comparison.txt`.
2. **Eliminate N+1 Queries:** Replace queries inside loops with a single batch query or join.
3. **Keep SQL SARGable:** Filter directly on indexed columns without wrapping them in functions.
4. **Batch Large Writes:** Chunk large updates/deletes (`LIMIT / TOP 2000`) to avoid locking tables.
