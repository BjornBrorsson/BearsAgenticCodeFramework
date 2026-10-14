# Phase: TEACH — Deep-Learning, Onboarding & Architectural Mentorship (Enterprise Edition)

> **Gatekeeper:** Read this file in full whenever the user wants to **learn, understand deeply, or be taught** how a domain, subsystem, data model, or framework works.
> **Difference from `phase_investigate.md`:** `phase_investigate.md` delivers a fast lookup answer; `phase_teach.md` builds durable mental models progressively in 5 layers.
> *(Load `../skills/teaching_and_onboarding.md` for lesson templates.)*

---

## The 5-Layer Teaching Method

1. **Layer 1 — Why & Mental Model (10,000-Meter View):** Explain the business problem solved, define the 3–5 core domain terms, and give a crisp mental model before showing code.
2. **Layer 2 — Visual Architecture & Data Flow:** Draw a Mermaid sequence or flowchart showing `[Caller/Trigger] → [Controller/Handler] → [Domain Service] → [DB Tables / External APIs]`.
3. **Layer 3 — Guided Code Walkthrough (The Red Thread):** Walk through the 3–5 critical source locations in execution order using verified **Durable Deep Links** (`SymbolName()` in `path` (`~L45`). `_Verified: YYYY-MM-DD._`), explaining *why* key invariants exist.
4. **Layer 4 — Common Pitfalls & Gotchas:** Highlight the top traps from `./docs/MASTER_MEMORIES.md` ("Naive approach vs. Correct repo pattern").
5. **Layer 5 — Hands-On Probe & Next Steps:** Provide a safe read-only query/command the learner can run right now to see the system in action, offer 2–3 follow-up drill-down paths, and update `./docs/wiki/<domain>.md` if new domain documentation was synthesized.
