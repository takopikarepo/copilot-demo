---
name: "Write Tests"
description: "Generate complete JUnit 5 + Mockito test suite for service class"
model: "gpt-4.1"
fallback_model: "claude-haiku"
context: ["file"]
tags: ["testing", "junit", "mockito", "spring-boot", "unit-test"]
version: "1.0.0"
ide_support: ["vscode", "intellij"]
author: "Java Team"
---

# Write Tests Prompt

> **How to use in VS Code**: Open the service class. In Copilot Chat, type `/write-tests`
> **How to use in IntelliJ**: Open the service class. Paste this prompt into Copilot Chat.
> **Model**: GPT-4.1 or Claude Haiku

---

## Prompt

Generate a complete test suite for the Java service class provided.

### What to generate

**1. Unit Test Class** using `@ExtendWith(MockitoExtension.class)`:
- Mock all dependencies with `@Mock`
- Inject mocks with `@InjectMocks`
- Test every public method
- Cover: happy path, null inputs, empty results, boundary conditions, exception cases

**2. Test method naming**: `methodName_scenario_expectedResult()`
- Example: `createUser_withDuplicateEmail_throwsConflictException()`
- Example: `findById_withValidId_returnsUserResponse()`
- Example: `findById_withUnknownId_throwsNotFoundException()`

**3. AAA Pattern** — strictly follow with blank lines between sections:
```java
// Arrange
[setup data and mocks]

// Act
[call the method under test]

// Assert
[verify result or exception]
```

**4. For each method, always test**:
- ✅ Valid input → expected output
- ❌ Invalid / null input → expected exception
- 📭 Empty result (if applicable) → correct handling
- 🔒 Unauthorised access (if applicable) → exception

**5. Mock setup patterns to use**:
```java
// Return value
when(repository.findById(userId)).thenReturn(Optional.of(entity));

// Empty result
when(repository.findById(unknownId)).thenReturn(Optional.empty());

// Throw exception
when(repository.save(any())).thenThrow(new DataIntegrityViolationException("duplicate"));

// Verify interaction
verify(repository, times(1)).save(any(UserEntity.class));
verify(repository, never()).delete(any());
```

**6. Test data builders** — create private helper methods:
```java
private UserEntity buildUserEntity() {
    // return test entity with valid data
}

private CreateUserRequest buildCreateUserRequest() {
    // return valid request DTO
}
```

### Rules
- Use `assertThat()` from AssertJ — not `assertEquals()` from JUnit directly
- Use `assertThrows()` for exception testing
- Never use `@SpringBootTest` in unit tests — pure Mockito only
- Import `org.mockito.Mockito.*` statically
- Import `org.assertj.core.api.Assertions.*` statically
- Class name: `[ServiceClassName]Test`
- Place in same package as the service under `src/test/java`
