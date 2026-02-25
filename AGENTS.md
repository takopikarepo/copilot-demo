# AGENTS.md

This file instructs AI coding agents (Codex, GitHub Copilot agent mode) on how to behave in this repository.

---

## Stack Context

- Java 21, Spring Boot 3.x, Gradle with Version Catalogs
- PostgreSQL + Spring Data JPA
- JWT + Spring Security 6.x
- JUnit 5, Mockito, Testcontainers
- Docker + Docker Compose

Always read existing code before generating new code. Match the patterns, naming conventions, and structure already in the project.

---

## ExecPlans

When working on **any complex feature, significant refactor, or cross-cutting change**, use an ExecPlan.

An ExecPlan is a structured markdown file that serves as both a design document and a living progress tracker. It allows the task to be paused and resumed purely from the plan file — no memory of prior conversation is needed.

### When to create an ExecPlan

Create an ExecPlan when the task involves any of the following:
- Creating a new feature end-to-end (entity → repo → service → controller → tests)
- Modifying more than 3 files
- A cross-cutting change (e.g. "add pagination to all list endpoints")
- A significant refactor
- Anything estimated to take more than 30 minutes

For small tasks (fixing a typo, adding a single field, answering a question) — do NOT create a plan, just do it.

### How to create and use an ExecPlan

1. Copy the template from `.agents/template/PLAN.md`
2. Save it to `.agents/<feature-name>/PLAN.md` (e.g. `.agents/user-address-management/PLAN.md`)
3. Fill in the plan by reading the codebase first — understand existing patterns before writing the plan
4. Present the plan to the user for review
5. Wait for confirmation: "do the plan" or "proceed"
6. Execute milestone by milestone
7. Keep the plan updated as you go — check off completed steps, log decisions, note surprises
8. The plan must always reflect current state so work can be restarted from it alone

### Rules for ExecPlan execution

- Do not ask "what should I do next?" — read the plan and proceed to the next milestone
- Commit after each milestone if git is available
- If you hit an ambiguity, resolve it autonomously and log your decision in the plan
- Update the Progress section after every stopping point
- Do not delete the plan file when done — it serves as a record

---

## Code Standards (Always Follow)

### Architecture
- Controller → Service → Repository — never skip layers
- Controllers: HTTP only, no business logic
- Services: all business logic, `@Transactional` on writes
- Repositories: data access only, no logic

### Java Style
- Constructor injection only — never `@Autowired` on fields
- `final` on all injected fields
- Use Lombok `@RequiredArgsConstructor` to reduce boilerplate
- DTOs must be Java Records
- Separate Request DTOs from Response DTOs
- Never expose JPA entities directly in REST responses
- Use `Optional<T>` — never return null from service methods

### Error Handling
- Create domain-specific exceptions: `ResourceNotFoundException`, `BusinessException`
- Use `@ControllerAdvice` + `@ExceptionHandler` for global handling
- Never swallow exceptions silently

### Testing
- Every new service method must have a unit test
- Unit tests: `@ExtendWith(MockitoExtension.class)` — Mockito only, no Spring context
- Integration tests: `@SpringBootTest` or Testcontainers
- Test naming: `methodName_scenario_expectedResult()`
- Follow AAA: Arrange / Act / Assert with blank lines between sections

### Security
- Never hardcode credentials, tokens, or secrets
- Always validate input with `@Valid` and Bean Validation
- Never log sensitive data

---

## Terminal Usage

You have access to the terminal. Use it freely for:
- `./gradlew compileJava` — verify compilation after generating code
- `./gradlew test --tests "ClassName"` — run specific tests
- `./gradlew test` — run full test suite
- `git diff --staged` — review what is staged
- `find . -name "*.java" | head -40` — explore project structure
- `cat path/to/file` — read existing files before generating similar ones

Always compile after generating code. Always run relevant tests after changes.
