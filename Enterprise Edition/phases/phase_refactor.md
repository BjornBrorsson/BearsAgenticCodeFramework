# Phase: REFACTOR — Behavior-Preserving Simplification & Golden Master Testing (Enterprise Edition)

> **Gatekeeper:** Read this file in full when asked to refactor, simplify, deduplicate, or modernize existing code, SQL, or service definitions without altering external behavior.

---

## The Golden Master Refactoring Loop

1. **Capture Baseline ("Before"):** Run the existing implementation against representative inputs and save exact outputs, row counts, logs, and timings in `./tasks/<task>/Verification/baseline/`.
2. **Lock External Invariants:** Document input contracts, output shapes, DB side effects, and error codes that must remain 100% identical.
3. **Simplify in Small, Reversible Steps:** Collapse duplicate branches, extract shared helpers, remove dead code, and preserve all intent/invariant comments. Do not mix functional rule changes into a structural refactor.
4. **Diff Outputs (`Before == After`):** Re-run the refactored code against the exact baseline inputs, verify 100% output parity and equal-or-better performance, and save `./Verification/00_REFACTOR_PARITY_REPORT.md`.
5. **Update Deep Links:** Repair any `./docs/wiki/<domain>.md` Durable Deep Links whose symbol names or line hints moved during refactoring.
