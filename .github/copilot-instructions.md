# GitHub Copilot Instructions — Java Development Team

> This file is automatically injected into every Copilot Chat session and inline suggestion.
> It defines our team standards, architecture rules, and what Copilot should always follow.

---

## Stack

- **Language**: Java 21 — use Records, Sealed Classes, Pattern Matching, Virtual Threads where applicable
- **Framework**: Spring Boot 3.x
- **Build**: Gradle with Version Catalogs (`libs.versions.toml`) — never hardcode dependency versions inline
- **Database**: PostgreSQL with Spring Data JPA / Hibernate
- **Auth**: JWT-based authentication with Spring Security 6.x
- **Containers**: Docker + Docker Compose
- **Testing**: JUnit 5, Mockito, Testcontainers for integration tests
- **Docs**: SpringDoc OpenAPI 3 for REST documentation

---

## Architecture — Layered Structure

Always follow this strict layering. Never skip layers or mix responsibilities.

```
HTTP Request
     ↓
 Controller          → handles HTTP only, delegates immediately to service
     ↓
  Service            → all business logic lives here
     ↓
 Repository          → data access only, no business logic
     ↓
Entity / Domain      → JPA entities, never exposed via REST
     ↓
DTO (Request/Response) → what goes in and out of the API
```

### Controller Rules
- Only handle HTTP: parse request, call service, return response
- No business logic whatsoever
- Use `@RestController`, `@RequestMapping`, `@Valid` on request bodies
- Return `ResponseEntity<T>` with explicit HTTP status codes

### Service Rules
- All business logic lives here — never in controllers or repositories
- Always define a `ServiceInterface` + `ServiceImpl` pair
- Use `@Service` on impl, `@Transactional` on methods that write to DB
- Throw domain exceptions, never return null

### Repository Rules
- Extend `JpaRepository<Entity, UUID>` or `JpaRepository<Entity, Long>`
- Use JPQL (`@Query`) for complex queries, never raw SQL unless necessary
- No business logic

### DTO Rules
- Use Java **Records** for all DTOs — they are immutable by default
- Separate `Request` DTOs from `Response` DTOs — never share them
- Apply Bean Validation annotations on Request DTOs (`@NotNull`, `@NotBlank`, `@Size`, etc.)
- Never expose JPA entities directly in REST responses

---

## Code Style

### Naming Conventions
- Classes: `PascalCase` — `UserService`, `ContractRepository`, `CreateUserRequest`
- Methods & Variables: `camelCase` — `findActiveContracts()`, `userId`
- Constants: `UPPER_SNAKE_CASE` — `MAX_RETRY_COUNT`, `DEFAULT_PAGE_SIZE`
- Packages: lowercase, domain-reversed — `com.company.module.feature`
- Test methods: `methodName_scenario_expectedResult` — `createUser_withDuplicateEmail_throwsException`

### Dependency Injection
- Always use **constructor injection** — never `@Autowired` on fields
- Mark injected fields as `final`
- Use Lombok `@RequiredArgsConstructor` to eliminate boilerplate constructors

### Exception Handling
- Create domain-specific exceptions: `ResourceNotFoundException`, `BusinessException`, `ValidationException`
- Use `@ControllerAdvice` + `@ExceptionHandler` for global error handling
- Return RFC 7807 Problem Detail format for all error responses
- Never swallow exceptions silently — always log or rethrow

### Logging
- Use SLF4J: `private static final Logger log = LoggerFactory.getLogger(ClassName.class);`
- Use Lombok `@Slf4j` as shorthand
- Never use `System.out.println`
- Use structured log messages: `log.info("User created: userId={}", userId)`
- Never log sensitive data: passwords, tokens, PII, card numbers

---

## What to Always Generate Together

When creating a new feature, always produce ALL of the following:

1. **Entity** — JPA entity with proper annotations, UUID primary key preferred
2. **Repository** — interface extending JpaRepository
3. **Request DTO** — Java Record with Bean Validation
4. **Response DTO** — Java Record
5. **Service Interface** — defines the contract
6. **Service Implementation** — business logic, `@Transactional` where needed
7. **REST Controller** — HTTP layer only
8. **Unit Test** — for service implementation using Mockito
9. **Integration Test skeleton** — `@SpringBootTest` or Testcontainers

---

## Testing Standards

- Unit tests: `@ExtendWith(MockitoExtension.class)` — mock all dependencies
- Integration tests: `@SpringBootTest` — test full stack
- Follow **AAA pattern**: Arrange / Act / Assert — with blank lines between sections
- Test method naming: `methodName_scenario_expectedResult()`
- Test ALL edge cases: null inputs, empty lists, duplicates, unauthorized access

---

## Security Rules

- Never hardcode credentials, tokens, secrets, or API keys
- Always validate AND sanitize all user input
- Use `@PreAuthorize` for method-level security
- Never log sensitive data
- Always check ownership before returning or modifying resources

---

## Spring Boot Config Rules

- Use `application.yml` — never `application.properties`
- Externalize ALL config — never hardcode URLs, ports, or timeouts
- Use `@ConfigurationProperties` for grouped config
- Separate profiles: `dev`, `test`, `prod`
