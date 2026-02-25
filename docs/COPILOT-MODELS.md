# GitHub Copilot — Model Selection Guide

Quick reference for choosing the right model for each task.

---

## Available Models

| Model | Speed | Best At |
|-------|-------|---------|
| **Claude Sonnet** | Medium | Architecture, complex generation, deep review |
| **GPT-4.1** | Fast | Refactoring, test generation, general coding |
| **Claude Haiku** | Fastest | Quick questions, inline help, simple tasks |
| **Codex (cdex 5.2)** | Slow | Multi-file autonomous execution, execPlan |

---

## When to Use What

### Use Claude Sonnet when:
- Running `feature-builder.agent.md` — full feature generation
- Running `pre-commit-review.agent.md` — thorough code review
- Running `code-auditor.agent.md` — full codebase analysis
- Running `explain-plan.prompt.md` — architecture decisions
- Complex business logic with multiple edge cases
- Designing a new module or service from scratch
- Security-sensitive code

### Use GPT-4.1 when:
- Running `write-tests.prompt.md` — test generation
- Running `test-runner.agent.md` — debugging test failures
- Refactoring existing code
- Running `add-endpoint.prompt.md` — adding endpoints
- General day-to-day coding tasks
- Explaining existing code

### Use Claude Haiku when:
- Quick inline code completions
- Simple questions: "what does this annotation do?"
- Writing Javadoc comments
- Renaming / small refactors
- Generating boilerplate (getters, toString, etc.)
- Quick YAML / config snippets

### Use Codex (cdex 5.2) when:
- You need execPlan — cross-cutting changes across many files
- "Add pagination to all list endpoints"
- "Add audit logging to all service methods"
- "Migrate all DTOs from classes to Records"
- "Add @PreAuthorize to all admin endpoints"
- Large-scale refactoring touching 10+ files
- Any change that follows a repeating pattern across the codebase

---

## Codex execPlan — How to Use

1. Switch model to **cdex 5.2** in Copilot Chat
2. Describe the cross-cutting change clearly:
   ```
   Add request/response logging to all REST controllers using
   a Spring HandlerInterceptor. Log: method, path, status code,
   duration. Use SLF4J. Do not log request bodies.
   ```
3. Codex will show you an **execution plan** — list of files it will change
4. **Review the plan** before approving
5. Approve → Codex executes all changes autonomously
6. Review the diff before committing

---

## Quick Decision Tree

```
Is it a cross-cutting change across many files?
  → Yes: Codex execPlan

Is it a full new feature from scratch?
  → Yes: Claude Sonnet + feature-builder agent

Is it tests, refactoring, or adding an endpoint?
  → Yes: GPT-4.1

Is it a quick question or simple completion?
  → Yes: Claude Haiku
```

---

## Codex execPlan Example Prompts

Try these with cdex 5.2:

```
Add @Operation (SpringDoc) annotations to all controller methods missing them.
```

```
Add soft delete (isActive boolean field) to all entities that currently hard delete.
```

```
Add request logging interceptor to log method, path, and duration for all endpoints.
```

```
Add pagination (Pageable) to all repository methods that return List<T>.
```

```
Ensure all service write methods have @Transactional — add where missing.
```
