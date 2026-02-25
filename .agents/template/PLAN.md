```md
# ExecPlan: [Feature Name]

## Overview

**Task**: [One sentence description of what needs to be built or changed]
**Type**: [ ] New Feature  [ ] Refactor  [ ] Bug Fix  [ ] Cross-cutting Change
**Estimated Complexity**: [ ] Simple (< 1hr)  [ ] Medium (half day)  [ ] Complex (1+ days)

---

## Context

[Read the existing codebase before filling this in. Describe:]
- What already exists that is relevant to this task
- What package/module this belongs to
- Any existing patterns this should follow
- Dependencies on other features or services

---

## Requirements

[Restate the requirement in your own words. Be specific:]
- What the feature must do
- What it must NOT do (constraints)
- Acceptance criteria (how we know it is done)

---

## Architecture Decisions

[Document each significant decision before writing code:]

| Decision | Chosen Approach | Alternatives Considered | Reason |
|----------|----------------|------------------------|--------|
| [e.g. DTO format] | [Java Record] | [regular class] | [immutability, less boilerplate] |

---

## Files Impact

### Files to Create

| File Path | Type | Purpose |
|-----------|------|---------|
| `src/main/java/.../model/FeatureEntity.java` | Entity | JPA entity for [...] |
| `src/main/java/.../repository/FeatureRepository.java` | Repository | Data access for [...] |
| `src/main/java/.../dto/CreateFeatureRequest.java` | Request DTO | Input validation for [...] |
| `src/main/java/.../dto/FeatureResponse.java` | Response DTO | API response shape |
| `src/main/java/.../service/FeatureService.java` | Service Interface | Contract definition |
| `src/main/java/.../service/FeatureServiceImpl.java` | Service Impl | Business logic |
| `src/main/java/.../controller/FeatureController.java` | Controller | REST endpoints |
| `src/test/java/.../service/FeatureServiceImplTest.java` | Unit Test | Service unit tests |

### Files to Modify

| File Path | Change Description |
|-----------|--------------------|
| `src/main/java/.../exception/GlobalExceptionHandler.java` | Add handler for new exception types |

---

## API Design

[Define the REST API before implementing:]

| Method | Path | Request Body | Response | Status |
|--------|------|-------------|----------|--------|
| POST | `/api/v1/features` | `CreateFeatureRequest` | `FeatureResponse` | 201 |
| GET | `/api/v1/features/{id}` | - | `FeatureResponse` | 200 |
| GET | `/api/v1/features` | - | `Page<FeatureResponse>` | 200 |
| PUT | `/api/v1/features/{id}` | `UpdateFeatureRequest` | `FeatureResponse` | 200 |
| DELETE | `/api/v1/features/{id}` | - | - | 204 |

---

## Things to Account For

[Before implementing, think through:]

- [ ] Security: Does this endpoint need authentication? Authorization checks?
- [ ] Validation: What input validation is needed? What are the edge cases?
- [ ] Transactions: Which service methods need `@Transactional`?
- [ ] Error cases: What exceptions can occur? Are they handled?
- [ ] Performance: Any N+1 query risks? Large dataset concerns?
- [ ] Breaking changes: Does this change any existing API contracts?
- [ ] DB migration: Is a schema change needed?

---

## Milestones

- [ ] **Milestone 1 — Research**: Read existing code, understand patterns, finalize this plan
- [ ] **Milestone 2 — Data Layer**: Create entity and repository
- [ ] **Milestone 3 — DTOs**: Create request and response records with validation
- [ ] **Milestone 4 — Service**: Create service interface and implementation
- [ ] **Milestone 5 — Controller**: Create REST controller with all endpoints
- [ ] **Milestone 6 — Exception Handling**: Add any new exceptions and handlers
- [ ] **Milestone 7 — Tests**: Write unit tests for service, verify all pass
- [ ] **Milestone 8 — Compile and Test**: Run `./gradlew compileJava` and `./gradlew test`
- [ ] **Milestone 9 — Review**: Self-review against team standards checklist

---

## Progress Log

| Timestamp | Milestone | Status | Notes |
|-----------|-----------|--------|-------|
| [date/time] | Research | ✅ Done | Found existing pattern in UserService |
| [date/time] | Data Layer | 🔄 In Progress | Entity created, repository pending |

---

## Decisions Log

| Decision | Reason |
|----------|--------|
| [e.g. Added soft delete instead of hard delete] | [Existing entities use soft delete pattern] |

---

## Self-Review Checklist

Before marking complete, verify:

- [ ] Constructor injection used everywhere — no field `@Autowired`
- [ ] All DTOs are Java Records
- [ ] Service interface + impl pair exists
- [ ] `@Transactional` on all write methods
- [ ] `@Valid` on all controller request bodies
- [ ] No JPA entities returned from controllers
- [ ] All new public service methods have unit tests
- [ ] Tests follow AAA pattern and `method_scenario_result` naming
- [ ] No hardcoded values — config is externalized
- [ ] No sensitive data in logs
- [ ] `./gradlew compileJava` passes ✅
- [ ] `./gradlew test` passes ✅

---

## Restart Instructions

If this plan needs to be resumed from scratch:

1. Read this entire PLAN.md file
2. Check the Progress Log to understand what has been completed
3. Read the files listed as already created in the Files to Create section
4. Resume from the first unchecked Milestone
5. Do not redo completed milestones unless the Progress Log indicates a problem
```
