# 🐻 Bear's Agentic Code Framework (BACF)

**A local-first, progressive-disclosure operational framework for autonomous AI coding agents.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Architecture: Progressive Disclosure](https://img.shields.io/badge/Architecture-Progressive_Disclosure-blue.svg)](#how-it-works-progressive-disclosure)
[![Editions: Enterprise & Startup](https://img.shields.io/badge/Editions-Enterprise_%7C_Startup-purple.svg)](#two-editions-choose-your-rigor)
[![Storage: 100% Markdown](https://img.shields.io/badge/Storage-Git--Native_Markdown-orange.svg)](#repository-structure)

---

## 🎯 Why BACF?

When working with AI coding agents (GitHub Copilot, Windsurf, Cline, Claude Code, Cursor, etc.) on real-world codebases, two failure modes inevitably appear:

1. **Instruction Dilution (Context Bloat):** Stuffing every rule, naming convention, database standard, and workflow into a single monolithic `.cursorrules` or system prompt overwhelms the model's attention mechanism. The agent forgets safety guardrails mid-task.
2. **Stateless Amnesia:** Agents rely on raw model intuition instead of verified system truth, repeating past mistakes, hallucinating schemas, and leaving zero audit trail for the next session.

**Bear's Agentic Code Framework (BACF)** solves this by treating agent instructions like modular software rather than a wall of text. An always-on entrypoint (`AGENT_RULES.md`) enforces core safety guardrails and dynamically routes the agent to load specific **Phase Runbooks** (`phases/`) and **Domain Skills** (`skills/`) only when needed.

---

## ⚖️ Two Editions: Choose Your Rigor

This repository provides two tailored versions of the framework so you can match the level of governance to your project's lifecycle:

* 🏢 **`Enterprise Edition/`**: Built for complex, regulated, or multi-service production environments (e.g., fintech, legacy databases, strict compliance). Emphasizes deep grounding, strict isolation, database migration safety, contract testing, and thorough documentation audit trails.
* 🚀 **`Startup Edition/`**: Streamlined for high-velocity greenfield projects, indie apps, and rapid prototyping. Retains the modular phase routing and persistent memory benefits while cutting out heavy ceremony for faster iteration.

---

## 🔄 How It Works: Progressive Disclosure

Instead of polluting the context window, BACF separates **Always-Active Guardrails** from **On-Demand Operational Context** across a 6-step execution loop (**ORIENT → ISOLATE → GROUND → BUILD → VERIFY → RECORD**):

```text
[ AGENT_RULES.md ] (Always in Context: Core Safety, Principles & Phase Router)
       │
       ├──► Blueprints (System & Workspace Setup)
       │      ├── agentic_rules_blueprint.md
       │      └── workspace_architecture_blueprint.md
       │
       ├──► phases/ (12 Operational Runbooks loaded dynamically by task type)
       │      ├── Discovery:  research, investigate, plan_and_design
       │      ├── Execution:  build, refactor, debug, hotfix, autonomous_backlog
       │      └── Closure:    test_and_validate, review, finalize, teach
       │
       └──► skills/ (17 Domain Modules loaded on-demand)
              └── Architecture, DB Scripts, Security, Kanban, Subagents, Testing, etc.

```

---

## 🧩 Included Modules

### 🧭 Phase Runbooks (`phases/`)

Each edition includes 12 specialized operational runbooks that dictate exact step-by-step protocols for the agent's current mode:

| Phase File | Purpose |
| --- | --- |
| `phase_research.md` | Grounding in existing code, wikis, and schemas before proposing changes. |
| `phase_investigate.md` | Root-cause analysis, log inspection, and system state diagnosis. |
| `phase_plan_and_design.md` | Architectural planning, breaking down specifications, and pre-implementation design. |
| `phase_build.md` | Core feature implementation rules, sandbox isolation, and safe code generation. |
| `phase_refactor.md` | Structural code improvements without altering external behavior. |
| `phase_debug.md` | Systematic bug reproduction, hypothesis testing, and targeted fixes. |
| `phase_hotfix.md` | Rapid, minimal-blast-radius emergency fixes for critical issues. |
| `phase_test_and_validate.md` | Verification against acceptance criteria, test suite execution, and edge-case checks. |
| `phase_review.md` | Self-review and peer-review checklists for code quality, security, and conventions. |
| `phase_finalize.md` | Memory logging, wiki updates, cleanup, and closing out tickets. |
| `phase_autonomous_backlog.md` | Unattended loop execution for chewing through queued Kanban tickets autonomously. |
| `phase_teach.md` | Explaining system architecture, onboarding developers, and generating learning material. |

### 🛠️ Domain Skills (`skills/`)

Agents dynamically load these 17 modular skill files only when their current task touches that specific domain:

* **Architecture & Planning:** `project_kickoff.md`, `requirements_and_spec_writing.md`, `architecture_and_premortem.md`
* **Code & Database Standards:** `conventions_and_naming.md`, `database_change_scripts.md`, `performance_and_query_tuning.md`, `security_privacy_and_compliance.md`
* **Testing & Verification:** `local_testing.md`, `api_and_contract_testing.md`, `autonomous_validation_and_artifacts.md`
* **Workflow & Agent Orchestration:** `kanban_workflow.md`, `subagent_orchestration.md`, `session_handoff_and_resume.md`, `git_and_release_delivery.md`
* **Knowledge & Onboarding:** `knowledge_wiki_and_memories.md`, `teaching_and_onboarding.md`

---

## 📂 Repository Structure

```text
BearsAgenticCodeFramework/
├── Enterprise Edition/
│   ├── AGENT_RULES.md                        # Core router & safety guardrails (Enterprise)
│   ├── agentic_rules_blueprint.md            # Meta-blueprint for rule design & routing
│   ├── workspace_architecture_blueprint.md   # Blueprint for setting up the workspace/vaults
│   ├── phases/                               # 12 Enterprise-grade phase runbooks
│   └── skills/                               # 17 Enterprise-grade domain skill modules
│
└── Startup Edition/
    ├── AGENT_RULES.md                        # Core router & safety guardrails (Startup)
    ├── agentic_rules_blueprint_SE.md         # Streamlined rules blueprint
    ├── workspace_architecture_blueprint_SE.md# Streamlined workspace blueprint
    ├── phases/                               # 12 High-velocity phase runbooks
    └── skills/                               # 17 Streamlined domain skill modules

```

---

## 🚀 Quick Start

### 1. Pick your Edition

Copy the contents of either `Enterprise Edition/` or `Startup Edition/` into your project's root (or into a dedicated `.agent/` / `docs/agent/` directory).

### 2. Initialize your Workspace Architecture

Read `workspace_architecture_blueprint.md` (or ask your AI agent to read it and scaffold the directories for you) to set up your local knowledge layer (`MASTER_MEMORIES.md`, Wiki/Schema indices, Kanban board folders, and isolated `WorkInProgress` sandboxes).

### 3. Connect your AI Coding Tool

Point your IDE or CLI agent's system instructions to `AGENT_RULES.md`:

* **GitHub Copilot (VS Code / CLI):** Reference `AGENT_RULES.md` in `.github/copilot-instructions.md`.
* **Windsurf:** Point `.windsurfrules` to `AGENT_RULES.md`.
* **Cline / Roo Code:** Set `AGENT_RULES.md` as your workspace custom instructions.
* **Claude Code:** Import `AGENT_RULES.md` in your root `CLAUDE.md`.

### 4. Customize your Skills

Tailor files like `skills/conventions_and_naming.md`, `skills/local_testing.md`, and `skills/database_change_scripts.md` to reflect your specific tech stack, linters, and build commands.

---

## 🤝 Contributing

New skill modules, phase refinements, and blueprint improvements are welcome! Open an issue or submit a PR.

## 📄 License

MIT License — free to use, adapt, and distribute.

```

```
