# GitHub Copilot Java Team Kit

A complete, production-ready setup for Java Spring Boot teams to get maximum productivity from GitHub Copilot. Drop the files from this repo into your project and your entire team immediately works faster, more consistently, and with less back-and-forth.

---

## Why This Exists

Without a shared setup, every developer uses Copilot differently. Some get great results, some get mediocre ones. Code review catches the same issues repeatedly because Copilot does not know your team standards. New developers do not know which prompts work well. There is no shared vocabulary between humans and the AI.

This kit solves all of that. It gives Copilot the context it needs to behave like a senior member of your specific team — one who knows your stack, your patterns, your rules, and your definition of done.

---

## What Is in This Repository

```
copilot-demo/
│
├── AGENTS.md                              # Root-level agent brain — teaches Codex what an ExecPlan is
│
├── .agents/
│   └── template/
│       └── PLAN.md                        # ExecPlan template — copied per feature at runtime
│
├── .github/
│   ├── copilot-instructions.md            # Auto-injected into every Copilot session
│   │
│   ├── agents/
│   │   ├── feature-builder.agent.md       # End-to-end feature generation
│   │   ├── pre-commit-review.agent.md     # Code review + test run before every commit
│   │   ├── test-runner.agent.md           # Run tests, fix failures, rerun
│   │   └── code-auditor.agent.md          # Full codebase standards audit
│   │
│   └── prompts/
│       ├── write-tests.prompt.md          # Generate test suite for any service class
│       ├── add-endpoint.prompt.md         # Add a REST endpoint with full wiring
│       ├── explain-plan.prompt.md         # Architecture plan before writing any code
│       ├── review-diff.prompt.md          # Code review from git diff
│       └── jira-story-to-code.prompt.md   # JIRA story to full implementation
│
└── docs/
    └── COPILOT-MODELS.md                  # Which model to use for which task
```

---

## The Four Building Blocks — Understand This First

Before looking at individual files, understand the four types of things in this kit and how they differ.

### 1. `copilot-instructions.md` — Always-On Context

This is a special file GitHub Copilot reads automatically at the start of every Copilot session for everyone on your team. You do not invoke it — it is always there, silently shaping every response Copilot gives.

Think of it as giving Copilot a permanent onboarding document about your team. Without it, Copilot gives generic Java code. With it, Copilot gives code that already uses your stack, your patterns, and your standards.

**Analogy**: The difference between a contractor who has never heard of your company versus one who read your internal wiki before showing up on day one.

### 2. Prompts (`.prompt.md`) — Saved, Reusable Instructions

A prompt file is a saved instruction any developer can invoke by name in Copilot Chat. It runs once, does what it says, and stops. You are still in control of every step.

Think of prompts as **smart, shared macros**. Instead of every developer typing the same long instruction from memory and getting slightly different results each time, prompts ensure everyone invokes the exact same well-crafted instruction.

**Analogy**: Keyboard shortcuts for complex Copilot instructions. One command, consistent results across the whole team.

### 3. Agents (`.agent.md`) — Autonomous Multi-Step Workers

An agent is different from a prompt. You give it a goal, not step-by-step instructions. It reads your codebase, makes decisions, creates files, runs terminal commands, checks compilation, runs tests, and reports back. You are not driving — the agent is.

**Analogy**: The difference between telling a junior developer "go to line 42 and add this method" (prompt) versus "implement the user address management feature following our standards and show me when it is done" (agent).

> **Important**: Agents with terminal execution only work in **VS Code**. IntelliJ users use the prompt files manually in chat instead.

### 4. `AGENTS.md` + ExecPlan (`PLAN.md`) — Structured Planning for Complex Tasks

This pattern comes from Aaron Friel's OpenAI DevDay 2025 talk "Shipping with Codex." `AGENTS.md` teaches the agent a shared vocabulary — specifically the word "ExecPlan." When you trigger this pattern, the agent creates a structured plan file (copied from your template), presents it to you for review, waits for your approval, then executes milestone by milestone — updating the plan as it goes.

The key insight: **the plan file is the source of truth, not the conversation**. The task can be paused, the conversation closed, and resumed later purely by reading the plan file. No prior conversation memory is needed.

**Analogy**: A contractor who writes a scope of work document, gets your sign-off, then builds against it — updating it as they go so you always know the current state.

---

## File-by-File Breakdown

---

### `.github/copilot-instructions.md`

**What it is**: A markdown file automatically injected into every Copilot Chat session for every developer on your team. No action required to activate it.

**Why it matters**: Without this, every developer gets a different version of Copilot depending on how well they prompt it. With this, the entire team gets a Copilot that already knows your Java version, framework, architecture rules, naming conventions, and what "done" means.

**What is in it**: Stack definition (Java 21, Spring Boot 3.x, Gradle Version Catalogs, PostgreSQL, JWT), strict layered architecture rules, code style rules (constructor injection, Records for DTOs, no null returns), the complete list of what to always generate together when building a feature, security rules, and testing standards.

**Without it**:
```
You: "Create a user service"
Copilot: Generates service with field @Autowired, returns null on not-found,
         no interface, no tests, uses String for ID
```

**With it**:
```
You: "Create a user service"
Copilot: Generates service interface + impl, constructor injection with
         @RequiredArgsConstructor, throws ResourceNotFoundException instead
         of returning null, UUID for ID, @Transactional on write methods,
         includes unit test skeleton
```

**How to activate**: Copy to `.github/copilot-instructions.md` in your project. Done. Everyone benefits immediately with zero action.

---

### `AGENTS.md`

**What it is**: A root-level markdown file that AI coding agents (Codex, Copilot agent mode) read to understand how to behave in your specific repository.

**Why it matters**: Without `AGENTS.md`, when you ask Codex to do something complex it has no idea what your codebase looks like or what patterns to follow. It might generate code in a completely different style from what you already have. With `AGENTS.md`, it reads your standards first and builds to match.

`AGENTS.md` does three things:
1. Defines the word **"ExecPlan"** as shared vocabulary — so when you say it, the agent knows exactly what to do
2. Tells the agent **when to plan vs. when to just act** (complex tasks → plan first; simple tasks → just do it)
3. Repeats your core code standards so the agent follows them during autonomous execution

**Without AGENTS.md**:
```
You: "Build a notification feature"
Codex: Immediately starts creating files in whatever structure it guesses,
       different naming from your existing code, no plan shown to you
```

**With AGENTS.md**:
```
You: "Build a notification feature"
Codex: Reads AGENTS.md → recognises this is complex → creates
       .agents/notification/PLAN.md → reads existing codebase first
       → fills in the plan → shows you the plan → waits for approval
```

**How to activate**: Place `AGENTS.md` in your project root. Codex reads it automatically.

---

### `.agents/template/PLAN.md` — The ExecPlan Template

**What it is**: A structured markdown template that gets copied for every new complex task. It becomes a living document tracking the entire lifecycle of a feature from design to completion.

**Why it matters**: For multi-hour or multi-day tasks, conversations hit context limits, developers switch tasks, and agents lose state. The ExecPlan solves this — the plan file is the single source of truth that can be restarted from zero context by reading the file alone. You can close the chat, come back tomorrow, say "resume the plan" and the agent picks up exactly where it left off.

**What is in it**:
- Overview: task type and complexity estimate
- Context: what already exists and what patterns to follow
- Requirements: restated clearly before any code is written
- Architecture Decisions: key decisions logged with alternatives considered and reasons
- Files Impact: every file to create or modify, listed before work starts
- API Design: all endpoints defined upfront
- Things to Account For: security, transactions, validation, performance, breaking changes
- Milestones: checkpoints that get checked off as work proceeds
- Progress Log: timestamp and status updated at every stopping point
- Decisions Log: deviations from the original plan documented with reasons
- Self-Review Checklist: quality gate to pass before declaring done
- Restart Instructions: exact steps for resuming with zero prior context

**Example workflow**:
```
Developer: "Build a user address management feature.
            Users can have multiple addresses, one marked as default."

Codex reads AGENTS.md → recognises this needs an ExecPlan
Copies .agents/template/PLAN.md → .agents/user-address-management/PLAN.md
Reads existing codebase → UserService.java, UserEntity.java, patterns

Fills in plan:

  Files to Create:
  - AddressEntity.java           entity with userId FK, isDefault flag
  - AddressRepository.java       findByUserIdAndIsDefaultTrue query
  - CreateAddressRequest.java    record: street, city, pincode, isDefault
  - AddressResponse.java         record: id, street, city, pincode, isDefault
  - AddressService.java          interface
  - AddressServiceImpl.java      enforce single default per user in same transaction
  - AddressController.java       POST/GET/PUT/DELETE /api/v1/users/{id}/addresses
  - AddressServiceImplTest.java

  Things to Account For:
  - When marking a new default: must unset previous default in same @Transactional
  - Deleting the default: prevent or auto-promote another address
  - Users can only manage their own addresses — ownership check required

Developer reviews → "looks good, do the plan"

Codex executes milestone by milestone, checking off progress:
  ✅ Milestone 1 — Research complete
  ✅ Milestone 2 — Entity + Repository created
  ✅ Milestone 3 — DTOs created
  🔄 Milestone 4 — Service (in progress)
  [ ] Milestone 5 — Controller
  [ ] Milestone 6 — Tests
  [ ] Milestone 7 — Compile and Test
```

If work stops at Milestone 4, a completely new session can resume from exactly that point.

**How to use**: The agent creates and fills this automatically when you describe a complex task. Manually trigger with: "create an ExecPlan for [task description]."

---

### `.github/agents/feature-builder.agent.md`

**What it is**: A VS Code Copilot agent that takes a plain English requirement and generates a complete Spring Boot feature across all layers — entity, repository, DTOs, service interface, service implementation, controller, and tests — then verifies it compiles.

**Why it matters**: Building a complete feature involves at minimum 8-9 files. Without this agent, developers write them one by one manually (slow) or ask Copilot for one at a time in separate prompts (loses context, inconsistent). This agent does everything in one go with full awareness of your existing codebase conventions.

**What it does step by step**:
1. Reads your existing code to understand naming conventions and patterns used in THIS project
2. Produces a written architecture plan listing every file it will create
3. Waits for your confirmation before touching anything
4. Generates all layers in the correct dependency order
5. Runs `./gradlew compileJava` to verify everything compiles
6. Reports a full summary with created files and available endpoints

**Example**:
```
You: Use the feature-builder agent.
     Requirement: I need a service contract management feature.
     Providers have contracts with customers. Each contract has a start date,
     end date, service type, and status (active/expired/cancelled).
     Admins can create and update. Both admins and customers can view.

Agent: ## Architecture Plan

  Files to Create:
  ServiceContractEntity.java      JPA entity linking provider and customer
  ServiceContractRepository.java  findByCustomerId, findByStatus queries
  CreateContractRequest.java      Record: providerId, customerId, startDate, endDate, serviceType
  ContractResponse.java           Record: id, status, dates, serviceType, customerName
  ServiceContractService.java     Interface: create, update, findById, findAll
  ServiceContractServiceImpl.java Business logic + @Transactional on writes
  ServiceContractController.java  POST, GET, PUT endpoints
  ServiceContractServiceImplTest  8 test scenarios

  Things to Account For:
  - Admin-only create/update needs @PreAuthorize("hasRole('ADMIN')")
  - endDate must be after startDate — validation in Request DTO
  - Consider scheduled job to auto-expire contracts past endDate

  Proceed? (yes/no)

You: yes

Agent: [generates all 8 files, places in correct packages]
       Running: ./gradlew compileJava
       ✅ Compilation successful — 0 errors

       Endpoints created:
       POST   /api/v1/contracts        Admin only
       GET    /api/v1/contracts/{id}   Admin + Customer
       PUT    /api/v1/contracts/{id}   Admin only
       GET    /api/v1/contracts        Paginated list
```

**How to use**: VS Code → Copilot Chat → Agent mode → describe your requirement → agent does the rest.

---

### `.github/agents/pre-commit-review.agent.md`

**What it is**: A VS Code Copilot agent that runs `git diff --staged`, reviews your code changes against team standards, identifies which tests are affected, runs those tests, and gives you a go/no-go recommendation before you commit.

**Why it matters**: PR reviews catch issues too late — the code is written, submitted, and feedback requires another cycle of changes. This agent gives you the same quality of review feedback before you even commit, when issues are cheapest to fix. It also runs the relevant tests so you know nothing is broken before the commit lands.

**What it does**:
1. Runs `git diff --staged` to get exactly what you are about to commit
2. Reviews each changed file against architecture rules, quality standards, and security checks
3. Identifies which test classes correspond to changed source files
4. Runs only those relevant tests: `./gradlew test --tests "AffectedTestClass"`
5. Reads the full test output including stack traces
6. Produces a structured report: blockers, warnings, suggestions, test results
7. Gives a clear APPROVED / APPROVED WITH WARNINGS / BLOCKED verdict

**Example**:
```
You: Use the pre-commit-review agent.

Agent: Running: git diff --staged
       Running: ./gradlew test --tests "UserServiceImplTest"

       ## Pre-Commit Review Report
       Status: ⚠️ APPROVED WITH WARNINGS

       ### ❌ Blockers (must fix before commit)
       - UserController.java:47 — Direct repository call from controller.
         Controllers must only call the service layer.
         Fix: Inject UserService instead of UserRepository here.

       ### ⚠️ Warnings (should fix)
       - UserServiceImpl.java:83 — createUser() has no test for the
         duplicate email scenario.
         Add: createUser_withDuplicateEmail_throwsConflictException()

       ### Test Results
       Tests Run: 12 | Passed: 11 | Failed: 1

       ### ❌ Failed Test
       UserServiceImplTest.updateUser_withValidData_returnsUpdatedUser
       Expected: "john.doe@example.com" but was: null
       Suggested Fix: Mapper not copying email field — check UserMapper.toResponse()

       Recommendation: BLOCKED
       Fix the controller violation and failing test before committing.
```

**How to use**: `git add` your changes → VS Code Copilot Chat → invoke the agent.

---

### `.github/agents/test-runner.agent.md`

**What it is**: A VS Code Copilot agent that runs your test suite, reads failures intelligently, explains what went wrong in plain English, suggests concrete fixes with before/after code, applies them with your approval, and reruns until everything is green.

**Why it matters**: Stack traces can be opaque. Developers often spend 20+ minutes on a failure that has a straightforward fix. This agent reads test output the way a senior developer would — identifies the root cause category, explains it clearly, and either fixes it or tells you exactly what to do.

**Example**:
```
You: Use the test-runner agent. Run all tests.

Agent: Running: ./gradlew test

       ## ❌ ContractServiceImplTest.createContract_validInput_returnsResponse

       Type: NullPointerException

       What happened: The mock for contractRepository.save() was not set up
       to return anything. When the service calls save(), Mockito returns null
       by default. The service then tries to call .getId() on null — NPE.

       Root Cause: Missing when() setup for repository.save()

       Suggested Fix — add to the Arrange section:
         when(contractRepository.save(any(ServiceContractEntity.class)))
             .thenReturn(savedContractEntity);

       Apply this fix? (yes/no)

You: yes

Agent: Fix applied.
       Running: ./gradlew test --tests "ContractServiceImplTest"
       ✅ All 8 tests passing.
```

**How to use**: VS Code Copilot Chat → invoke agent → specify a class or run everything.

---

### `.github/agents/code-auditor.agent.md`

**What it is**: A VS Code Copilot agent that scans your entire codebase for violations of team standards, prioritises findings by severity (Critical / High / Medium / Low), and produces a structured report with concrete fixes for every issue found.

**Why it matters**: Standards drift happens gradually and invisibly. One developer uses field injection. Another skips the service interface. A third hardcodes a URL. None of it gets caught in PR review because reviewers focus on business logic. Over time the codebase becomes inconsistent and expensive to maintain. This agent surfaces all of it in one report.

**Example**:
```
You: Use the code-auditor agent. Audit the entire project.

Agent: Scanning 47 Java files...
       Running: ./gradlew compileJava
       Running: ./gradlew test

       ## Code Audit Report — 47 files scanned
       🔴 Critical: 1 | 🟠 High: 3 | 🟡 Medium: 5 | 🔵 Low: 2

       ### 🔴 Critical
       PaymentController.java:23 — Hardcoded API secret in source code
         String apiKey = "sk-live-abc123xyz"
         Risk: Credential visible to anyone with repo access, including git history
         Fix: Move to application.yml environment variable,
              inject via @ConfigurationProperties

       ### 🟠 High
       InvoiceService.java — No service interface exists
         InvoiceServiceImpl.java exists but there is no InvoiceService interface
         Fix: Create InvoiceService interface, have Impl implement it

       UserController.java:67 — Business logic in controller
         Age validation logic at line 67 belongs in the service layer
         Fix: Move to UserServiceImpl.validateUserAge()

       ### Architecture Health
       Layering respected     ⚠️  one controller calls repository directly
       DTOs are Records       ✅
       Constructor injection  ✅
       No hardcoded config    ❌
       Version catalog used   ✅
```

**How to use**: VS Code Copilot Chat → invoke agent. Best run weekly or before major releases.

---

### `.github/prompts/write-tests.prompt.md`

**What it is**: A reusable prompt that generates a complete JUnit 5 + Mockito test suite for any service class — covering happy paths, null inputs, empty results, boundary conditions, and all exception cases.

**Why it matters**: Tests are the most commonly skipped step under time pressure. This prompt makes writing tests nearly as fast as writing the service itself, while enforcing consistent testing conventions. Every developer on the team produces tests that look the same and cover the same categories of scenarios.

**What it generates**:
- Test class with `@ExtendWith(MockitoExtension.class)`
- `@Mock` for every dependency, `@InjectMocks` for the class under test
- Private test data builder methods (`buildEntity()`, `buildRequest()`)
- Tests for every public method covering: valid input, null input, empty results, exceptions
- Correct naming convention: `methodName_scenario_expectedResult()`
- Strict AAA pattern with blank lines between sections
- AssertJ assertions (`assertThat`) throughout

**Example**:
```
[AddressServiceImpl.java open in editor]
/write-tests

Generated: AddressServiceImplTest.java

@ExtendWith(MockitoExtension.class)
class AddressServiceImplTest {

    @Mock private AddressRepository addressRepository;
    @Mock private UserRepository userRepository;
    @InjectMocks private AddressServiceImpl addressService;

    @Test
    void createAddress_validInput_returnsAddressResponse() {
        // Arrange
        CreateAddressRequest request = buildCreateAddressRequest();
        when(userRepository.existsById(USER_ID)).thenReturn(true);
        when(addressRepository.save(any())).thenReturn(buildAddressEntity());

        // Act
        AddressResponse result = addressService.createAddress(USER_ID, request);

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.city()).isEqualTo("Hyderabad");
        verify(addressRepository, times(1)).save(any());
    }

    @Test
    void createAddress_userNotFound_throwsResourceNotFoundException() {
        // Arrange
        when(userRepository.existsById(UNKNOWN_ID)).thenReturn(false);

        // Act & Assert
        assertThrows(ResourceNotFoundException.class,
            () -> addressService.createAddress(UNKNOWN_ID, buildCreateAddressRequest()));
    }
}
```

**VS Code**: Open the service file → Copilot Chat → `/write-tests`
**IntelliJ**: Open the service file → Copilot Chat → paste prompt content → send

---

### `.github/prompts/add-endpoint.prompt.md`

**What it is**: A prompt that adds a single REST endpoint to an existing feature — generating the request DTO, updating the service interface and implementation, wiring the controller method, and adding a unit test.

**Why it matters**: Adding an endpoint requires touching 4-5 files consistently. Without this prompt, Copilot often produces inconsistent DTO naming, missing `@Valid` annotations, wrong HTTP status codes, or forgets to update the service interface. This prompt ensures all pieces are connected correctly every single time.

**Example**:
```
/add-endpoint

Add a PATCH endpoint to partially update a service contract status.
Only admins can do this. Valid statuses are ACTIVE, CANCELLED, EXPIRED.
```

Copilot will:
- Create `UpdateContractStatusRequest.java` Record with `@NotNull ContractStatus status`
- Add `updateStatus(UUID id, UpdateContractStatusRequest request)` to `ServiceContractService` interface
- Implement in `ServiceContractServiceImpl` with `@Transactional` and status validation
- Add `PATCH /api/v1/contracts/{id}/status` to controller with `@PreAuthorize("hasRole('ADMIN')")`
- Add unit test: `updateStatus_validStatus_returnsUpdatedContract()`

---

### `.github/prompts/explain-plan.prompt.md`

**What it is**: A prompt that produces a detailed architecture plan — listing every file that needs to change, every key decision to make, every edge case to consider — before a single line of code is written.

**Why it matters**: The most expensive mistakes are architecture decisions made incorrectly and caught late. Starting to code before thinking through transaction boundaries, ownership checks, cascade deletes, or breaking changes means refactoring later under time pressure. This prompt forces that thinking upfront and creates a shared understanding between the developer and the AI before any code is produced.

**Example**:
```
/explain-plan

I need to add soft delete to users. Deactivated users cannot log in.
Admins can reactivate deactivated users.

Copilot responds:

  Affected Layers:
  ✅ Entity       — add isActive boolean field, default true
  ✅ Repository   — update all queries to filter where isActive = true
  ✅ Service      — deactivate() sets false, add reactivate() method
  ✅ Controller   — PATCH /api/v1/users/{id}/deactivate and /reactivate
  ✅ Security     — JWT auth filter must check isActive before issuing token

  Files to Modify:
  UserEntity.java              add isActive = true field
  UserRepository.java          update findAll, findByEmail to filter inactive
  UserServiceImpl.java         add deactivate() and reactivate() methods
  UserController.java          add two new PATCH endpoints
  JwtAuthenticationFilter.java add isActive check in token validation

  Things to Account For:
  - DB migration: ALTER TABLE users ADD COLUMN is_active BOOLEAN DEFAULT TRUE
  - Inactive user login should return 403 Forbidden, not 401 Unauthorized
  - Should deactivating a user suspend their active contracts?

  Complexity: Medium (half day)

  Shall I proceed with code generation?
```

---

### `.github/prompts/review-diff.prompt.md`

**What it is**: A prompt that performs structured code review of your staged git changes. In VS Code it uses the `#changes` variable to automatically capture your diff. In IntelliJ you paste the diff output manually.

**Why it matters**: Most code review happens at PR time — after the code is written, tested, and submitted. Getting architectural feedback at that stage is expensive and demoralising. This prompt gives you review-quality feedback before you even commit, at the moment when issues are cheapest and fastest to fix.

**Example in VS Code**:
```
/review-diff #changes

## Code Review

Overall: ⚠️ Approved with comments

### ❌ Must Fix
- AddressController.java:34 — Missing @Valid on @RequestBody parameter.
  Bean Validation annotations on the Record will not trigger without @Valid.
  Fix: public ResponseEntity<AddressResponse> create(@Valid @RequestBody CreateAddressRequest request)

### ⚠️ Should Fix
- AddressServiceImpl.java:61 — setDefaultAddress() modifies two entities
  but has no @Transactional annotation. If the second save fails you will
  end up with two default addresses in the database.
  Fix: Add @Transactional to this method.

### What Looks Good
- Records used for all DTOs ✅
- Constructor injection throughout ✅
- ResourceNotFoundException thrown correctly on not-found ✅
```

**VS Code**: Stage changes → Copilot Chat → `/review-diff #changes`
**IntelliJ**: Run `git diff --staged` in terminal → copy output → paste into Copilot Chat with prompt

---

### `.github/prompts/jira-story-to-code.prompt.md`

**What it is**: A prompt that takes a JIRA story including acceptance criteria and converts it into a complete implementation — planning first, generating all layers, and producing an explicit traceability table mapping every acceptance criterion to the specific code and test that satisfies it.

**Why it matters**: There is often a gap between what the story specifies and what gets built. Acceptance criteria get partially implemented. Tests do not cover all the scenarios listed. This prompt closes that gap explicitly — you can verify that every criterion in the story has both code and a test before declaring the story done.

**Example**:
```
/jira-story-to-code

Story: PROJ-234 — Contract Renewal Notification
As an admin, I want to be notified 30 days before a service contract expires
so I can reach out about renewal.

Acceptance Criteria:
- System checks daily for contracts expiring within 30 days
- Admin receives an email notification with contract details
- No duplicate notifications for the same contract in the same window
- Email includes: customer name, contract ID, expiry date, service type
- If no contracts are expiring, no email is sent

After generating code, Copilot outputs:

  Acceptance Criteria Traceability:
  | Criterion                      | Implemented In                     | Test Method                          |
  |--------------------------------|------------------------------------|--------------------------------------|
  | Check daily                    | ContractExpiryScheduler @Scheduled | scheduler_runsDaily_triggersCheck()  |
  | Admin email notification       | NotificationService.sendAlert()    | sendAlert_validContract_sendsEmail() |
  | No duplicate notifications     | NotificationRepo.existsByContract  | sendAlert_alreadyNotified_skips()    |
  | Email has all required fields  | ExpiryEmailTemplate                | emailTemplate_containsAllFields()    |
  | No email if nothing expiring   | Scheduler null guard               | scheduler_noneExpiring_noEmail()     |
```

Every criterion is covered. Every criterion has a test.

---

### `docs/COPILOT-MODELS.md`

**What it is**: A quick reference guide for the team on which model to choose for which type of task, including a decision tree and specific example prompts for each model.

**Why it matters**: Developers who default to the same model for everything get inconsistent results. Claude Sonnet is significantly better at architecture and complex generation. Claude Haiku is much faster for quick tasks where quality is less critical. Codex is the only option for true autonomous multi-file execution. Using the right model for the task makes a meaningful difference in both quality and speed.

**The short version**:

| Task | Model |
|------|-------|
| Architecture decisions, full feature generation, deep review | **Claude Sonnet** |
| Test generation, refactoring, adding endpoints | **GPT-4.1** |
| Quick questions, inline help, Javadoc, small tasks | **Claude Haiku** |
| Cross-cutting changes across 10+ files | **Codex (cdex 5.2)** |

---

## How to Install in Your Java Project

```bash
# From the root of this repo, copy everything into your project
cp -r .github/    /path/to/your/java-project/
cp    AGENTS.md   /path/to/your/java-project/
cp -r .agents/    /path/to/your/java-project/
```

`copilot-instructions.md` activates automatically for everyone. Agents and prompts appear in VS Code immediately. `AGENTS.md` is active for Codex from the moment you place it.

---

## Daily Workflow

```
Morning — pick up a JIRA story
  /explain-plan             understand the full impact before touching code
  /jira-story-to-code       scaffold from the acceptance criteria

Building a new feature
  feature-builder agent     full end-to-end generation with plan + compile verify

During development
  /add-endpoint             wire up individual endpoints as needed
  /write-tests              generate tests after completing each service method

Before every commit
  pre-commit-review agent   review + run relevant tests + get verdict
  fix any blockers flagged
  commit clean

Cross-cutting changes (touching 10+ files)
  Switch to Codex cdex 5.2
  Describe the change clearly
  Review the execPlan Codex produces
  Approve → Codex executes across all files
  Review the diff before committing

Weekly
  code-auditor agent        catch standards drift before it accumulates
```

---

## IDE Compatibility

| Feature | VS Code | IntelliJ |
|---------|:-------:|:--------:|
| `copilot-instructions.md` auto-inject | ✅ | ✅ |
| `AGENTS.md` read by Codex | ✅ | ✅ |
| Prompt files via `/` picker | ✅ | Manual paste |
| Agent files (autonomous execution) | ✅ | ❌ |
| Terminal commands from agents | ✅ | ❌ |
| `#changes` git diff variable | ✅ | Manual paste |
| Inline code suggestions | ✅ | ✅ |
| Copilot Chat | ✅ | ✅ |

**IntelliJ developers**: Open the relevant `.prompt.md` file, copy the prompt section, paste into Copilot Chat alongside your code context. The quality of output is the same — just without the automatic invocation and terminal execution that VS Code agent mode provides.

**Recommendation**: Use IntelliJ for daily development with inline suggestions and manual prompt use. Switch to VS Code for agent-heavy sessions — full feature generation, pre-commit review with test runs, or Codex execPlan tasks.

---

## Contributing

When you find a prompt or agent workflow that works particularly well, add it here so the whole team benefits.

Copy an existing `.prompt.md` or `.agent.md` as a starting point. Include a real example of the input you gave and the output you got so the team can evaluate it before adopting it. Raise a PR with your addition.
