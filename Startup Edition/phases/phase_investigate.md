# Phase: INVESTIGATE — "How Does X Work?" & "Why Does Y Behave Like This?" (Startup Edition)

> **Gatekeeper:** Read this file in full when the user asks an **explanatory or exploratory question** — *"How does X work?"*, *"Why is Y not working like this or that?"*, or *"Where does Z happen?"*
> **No Setup Friction:** Do not create `./tasks/` folders or `Tickets/` cards just to answer a question — investigate directly and save reusable findings to the wiki/memories.

---

## Investigation Loop

1. **ORIENT First:** Check `./docs/DOCUMENTATION_INDEX.md`, `./docs/wiki/<feature>.md`, `./docs/MASTER_MEMORIES.md`, and `./docs/schemas/SCHEMA_INDEX.md` before grepping the codebase.
2. **Trace the Real Code Path (No Guessing):** Follow the actual chain from `Route / UI / Trigger → Handler / Service → Model / SQL / External API`. If checking a database, use read-only `SELECT` queries.
3. **Explain the "Why" (Expectation vs. Reality):** Point to the exact `if` condition, config/env variable, schema constraint, or library quirk that causes Y to behave differently than expected.
4. **Cite Durable Deep Links:** Give a concise TL;DR, the call flow, and symbol-anchored links (`symbolName()` in `src/...` (`~L42`). `_Verified: YYYY-MM-DD._`).
5. **Persist Reusable Knowledge:**
   * Undocumented feature flow discovered? → Create or update `./docs/wiki/<feature>.md`.
   * Tricky bug/quirk uncovered? → Add it to `./docs/MASTER_MEMORIES.md` so you never have to re-investigate it.
