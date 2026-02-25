# Add Endpoint Prompt

> **How to use in VS Code**: In Copilot Chat, type `/add-endpoint` then describe the endpoint
> **How to use in IntelliJ**: Paste this prompt into Copilot Chat with your description
> **Model**: GPT-4.1 or Claude Sonnet

---

## Prompt

Add a new REST endpoint to the existing Spring Boot application based on the requirement described.

### Steps to follow

**1. Read existing code first**
- Find the relevant Controller, Service Interface, Service Implementation, and Repository
- Match the exact code style, package structure, and patterns already used
- Use the same DTO naming conventions as existing code

**2. What to generate for every new endpoint**

**Request DTO** (Java Record with validation):
```java
public record UpdateUserProfileRequest(
    @NotBlank(message = "Name cannot be blank")
    String name,

    @Email(message = "Must be a valid email")
    String email
) {}
```

**Response DTO** (Java Record) — reuse existing response DTO if appropriate.

**Service Interface** — add method signature only.

**Service Implementation** — add business logic:
- Fetch entity, validate rules, update, save, map to response
- Add `@Transactional` on write operations
- Throw `ResourceNotFoundException` if resource not found

**Controller** — add endpoint method:
- Correct HTTP method (GET, POST, PUT, PATCH, DELETE)
- Correct path with path variables
- `@Valid` on request body
- Return `ResponseEntity<T>` with correct status code
- Add `@Operation` (SpringDoc) annotation for API docs

**Unit Test** — add test for the new service method following team test pattern.

### HTTP Status Code Rules
- `GET` success → `200 OK`
- `POST` creation → `201 Created`
- `PUT` / `PATCH` update → `200 OK`
- `DELETE` → `204 No Content`
- Not found → `404` (via exception handler)
- Validation failure → `400`
- Unauthorised → `401`
- Forbidden → `403`

### Rules
- Never modify more than what is needed for this endpoint
- Never introduce a new pattern if an existing one already exists
- Always add `@Valid` on `@RequestBody`
- Never return the JPA entity — always map to a Response DTO
