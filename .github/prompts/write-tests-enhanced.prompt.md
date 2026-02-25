---
name: "Write Tests (Enhanced)"
description: "Generate complete JUnit 5 + Mockito test suite for service class"
model: "gpt-4.1"
context: ["file"]
tags: ["testing", "junit", "mockito", "spring-boot", "unit-test"]
version: "1.0.0"
ide_support: ["vscode", "intellij"]
author: "Java Team"
---

# Write Tests Prompt (Enhanced with YAML Front Matter)

> **Model**: GPT-4.1 or Claude Haiku
> **Context needed**: Open service class file in editor
> **IntelliJ**: Right-click → AI Actions → Write Tests (Enhanced)
> **VS Code**: Copilot Chat → `/write-tests-enhanced`

---

## What This Prompt Does

Generates a complete test suite for the Java service class provided.

---

## What Will Be Generated

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

---

## Rules

- Use `assertThat()` from AssertJ — not `assertEquals()` from JUnit directly
- Use `assertThrows()` for exception testing
- Never use `@SpringBootTest` in unit tests — pure Mockito only
- Import `org.mockito.Mockito.*` statically
- Import `org.assertj.core.api.Assertions.*` statically
- Class name: `[ServiceClassName]Test`
- Place in same package as the service under `src/test/java`

---

## Example Output

For `UserServiceImpl.java`, generate:

```java
package com.company.user.service;

import com.company.user.dto.CreateUserRequest;
import com.company.user.dto.UserResponse;
import com.company.user.entity.UserEntity;
import com.company.user.exception.ResourceNotFoundException;
import com.company.user.repository.UserRepository;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;
import java.util.UUID;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class UserServiceImplTest {

    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserServiceImpl userService;

    private static final UUID USER_ID = UUID.randomUUID();

    @Test
    void createUser_withValidInput_returnsUserResponse() {
        // Arrange
        CreateUserRequest request = buildCreateUserRequest();
        UserEntity entity = buildUserEntity();
        when(userRepository.existsByEmail(request.email())).thenReturn(false);
        when(userRepository.save(any(UserEntity.class))).thenReturn(entity);

        // Act
        UserResponse result = userService.createUser(request);

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.email()).isEqualTo("test@example.com");
        verify(userRepository, times(1)).save(any(UserEntity.class));
    }

    @Test
    void createUser_withDuplicateEmail_throwsConflictException() {
        // Arrange
        CreateUserRequest request = buildCreateUserRequest();
        when(userRepository.existsByEmail(request.email())).thenReturn(true);

        // Act & Assert
        assertThrows(ConflictException.class, 
            () -> userService.createUser(request));
        verify(userRepository, never()).save(any());
    }

    @Test
    void findById_withValidId_returnsUserResponse() {
        // Arrange
        UserEntity entity = buildUserEntity();
        when(userRepository.findById(USER_ID)).thenReturn(Optional.of(entity));

        // Act
        UserResponse result = userService.findById(USER_ID);

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.id()).isEqualTo(USER_ID);
    }

    @Test
    void findById_withUnknownId_throwsNotFoundException() {
        // Arrange
        when(userRepository.findById(USER_ID)).thenReturn(Optional.empty());

        // Act & Assert
        assertThrows(ResourceNotFoundException.class,
            () -> userService.findById(USER_ID));
    }

    private UserEntity buildUserEntity() {
        UserEntity entity = new UserEntity();
        entity.setId(USER_ID);
        entity.setEmail("test@example.com");
        entity.setName("Test User");
        return entity;
    }

    private CreateUserRequest buildCreateUserRequest() {
        return new CreateUserRequest(
            "test@example.com",
            "Test User",
            "password123"
        );
    }
}
```

---

## IntelliJ-Specific Usage

Thanks to the YAML front matter at the top of this file, IntelliJ can:
1. **Auto-discover** this prompt in the AI Actions menu
2. **Show metadata** (name, description, model) in the UI
3. **Auto-inject context** - the current file you have open
4. **Version track** - know which version of the prompt is being used

**To use in IntelliJ:**
1. Open your service implementation file (e.g., `UserServiceImpl.java`)
2. Right-click anywhere in the editor
3. Select "AI Actions" → "Write Tests (Enhanced)"
4. Review generated test code
5. Accept or modify as needed
