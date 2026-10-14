# Skill: Sub-Agent Orchestration & Parallel Execution (Startup Edition)

> **Load When:** Your agent runtime supports sub-agents and you are doing broad repo exploration, specifying multiple V1 tickets, or working through independent tasks in parallel.

---

## Rules for Using Sub-Agents Effectively

1. **Protect Main Context:** Use read-only explorer sub-agents for broad codebase searches or multi-folder audits so only a short summary + Durable Deep Links (`symbolName()` in `src/...` (`~L42`). `_Verified: YYYY-MM-DD._`) enters the main chat.
2. **Parallelize Disjoint Work:** Spawn parallel sub-agents to draft independent `Tickets/Backlog/` cards or implement features that touch **completely separate files**.
3. **Zero File Collisions:** Never let two parallel sub-agents edit the same file, claim the same Kanban card, or edit `PROJECT_LOG.md` at the same time — have the main agent merge log updates.
4. **Front-Load Safety in Every Sub-Agent Prompt:** Sub-agents are stateless; always remind them: no secrets in code/git, dev outbound comms muted, no destructive SQL on shared/prod DBs, and real verification before done.
