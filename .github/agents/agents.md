# 🤖 Agent Registry

> Reference document describing all agents, their responsibilities, and how they interact.
> Intended for developers onboarding to this project.

---

## Overview

This project uses a multi-agent workflow powered by GitHub Copilot custom agents. When a Jira story is ready for development, the **Orchestrator** coordinates all other agents in sequence, with `plan.md` acting as the shared state between them.

```
Developer pastes Jira story to Orchestrator
             ↓
     Orchestrator reads plan.md
             ↓
     ┌───────────────────────────────────┐
     │ No plan?     → invoke Planner     │
     │ Plan ready?  → invoke Coder       │
     │ Code done?   → invoke Tester      │
     │ Tests pass?  → invoke Documenter  │
     │ All done?    → summarize & close  │
     └───────────────────────────────────┘
             ↓
     Each agent updates plan.md
```

---

## Agents

### 🎯 Orchestrator — `.github/agents/orchestrator.md`
**Purpose:** Coordinates the entire workflow. Reads `plan.md` to determine the current state and delegates to the correct agent. Never implements anything itself.

**Invoke:** Select **Orchestrator** in Copilot Chat → paste Jira story
**Input:** Jira story (ID, description, acceptance criteria)
**Output:** Delegates to sub-agents, final summary when done

---

### 🧠 Planner — `.github/agents/planner.md`
**Purpose:** Translates a Jira story into a concrete, ordered task list. Scans the codebase for similar patterns and references them in each task so the Coder knows where to look.

**Invoke:** Called by Orchestrator, or manually select **Planner** in Copilot Chat
**Input:** Jira story
**Output:** Populated `plan.md` with structured tasks

---

### 💻 Coder — `.github/agents/coder.md`
**Purpose:** Implements tasks from `plan.md`. Reads similar existing files before writing anything, follows project conventions, and uses modern Java 21 + Spring Boot + MongoDB patterns.

**Invoke:** Called by Orchestrator, or manually select **Coder** in Copilot Chat
**Input:** Tasks from `plan.md`
**Output:** Implemented code, updated `plan.md` task statuses

**Stack:**
- Java 21 (virtual threads, records, pattern matching, sealed classes)
- Spring Boot (controller → service → repository layering)
- MongoDB (`MongoRepository` / `ReactiveMongoRepository`)
- Streams over imperative loops
- Design patterns: Strategy, Builder, Factory, Decorator, AOP

---

### 🧪 Tester — `.github/agents/tester.md`
**Purpose:** Writes unit and integration tests for all implemented code. Maps every acceptance criterion to at least one test case.

**Invoke:** Called by Orchestrator, or manually select **Tester** in Copilot Chat
**Input:** Implemented files + tasks from `plan.md`
**Output:** Test classes, updated `plan.md` with AC coverage

**Test stack:** JUnit 5, Mockito, Spring Boot Test, MockMvc, AssertJ, `@DataMongoTest`

---

### 📝 Documenter — `.github/agents/documenter.md`
**Purpose:** Adds Javadoc to public classes and methods, updates `README.md` for new endpoints, and adds inline comments only where logic is non-obvious.

**Invoke:** Called by Orchestrator, or manually select **Documenter** in Copilot Chat
**Input:** Implemented + tested files, tasks from `plan.md`
**Output:** Documented code, updated `README.md`, `plan.md` marked fully done

---

## Shared State: `plan.md`

All agents read and write `plan.md`. It tracks:
- The active story (ID, title, status)
- Acceptance criteria with checkboxes
- Task list with statuses (`[ ]` → `[~]` → `[x]`)
- Test coverage mapping
- Documentation checklist
- Notes and blockers

---

## How to Use

### Full automated flow
1. Open Copilot Chat in VS Code
2. Select **Orchestrator** from the agent dropdown
3. Paste your Jira story
4. The Orchestrator will coordinate everything — just follow its prompts

### Skip to a specific agent (if plan already exists)
1. Open Copilot Chat
2. Select the agent you need (e.g. **Coder**)
3. Tell it: `plan.md is ready, please implement the tasks for JIRA-XXX`

### Resume interrupted work
1. Open Copilot Chat
2. Select **Orchestrator**
3. Say: `Resume JIRA-XXX — check plan.md for current state`

---

## Adding New Agents

To add a new agent:
1. Create `.github/agents/your-agent.md` with its role, responsibilities, and rules
2. Register it in this file under **Agents**
3. Update `orchestrator.md` decision logic to include when to invoke it
