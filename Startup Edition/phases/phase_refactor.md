# Phase: REFACTOR — Behavior-Preserving Cleanup & Golden Master Diffing (Startup Edition)

> **Gatekeeper:** Read this file in full when refactoring, simplifying, or cleaning up existing code without changing behavior.

---

## Golden Master Loop

1. **Capture Baseline ("Before"):** Run the current code on representative inputs and save outputs/logs in `./tasks/<feature>/Verification/baseline/`.
2. **Simplify in Small Steps:** Remove duplication, flatten nesting, and keep intent comments. Never mix feature changes into a pure refactor.
3. **Verify `Before == After`:** Re-run on the same inputs, prove 100% identical outputs in `./Verification/`, and update any moved Durable Deep Links in `./docs/wiki/<feature>.md`.
