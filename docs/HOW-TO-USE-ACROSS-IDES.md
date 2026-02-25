# How to Use Agents, Prompts & ExecPlans Across Different IDEs

Complete practical guide for using this repository's features in VS Code and IntelliJ IDEA.

---

## Table of Contents

1. [Setup & Installation](#setup--installation)
2. [Using Prompts in VS Code](#using-prompts-in-vs-code)
3. [Using Prompts in IntelliJ](#using-prompts-in-intellij)
4. [Using Agents in VS Code](#using-agents-in-vs-code)
5. [Agents in IntelliJ (Alternatives)](#agents-in-intellij-alternatives)
6. [Working with ExecPlans](#working-with-execplans)
7. [Complete Workflow Examples](#complete-workflow-examples)
8. [Troubleshooting](#troubleshooting)

---

## Setup & Installation

### Prerequisites

**Required:**
- GitHub Copilot subscription
- Java Spring Boot project
- Git repository

**IDE Options:**
- VS Code (full feature support)
- IntelliJ IDEA (partial support - prompts only)

### Installation Steps

**Step 1: Copy files to your project**
```bash
cd /path/to/your-spring-boot-project

# Copy all GitHub Copilot configuration
cp -r /path/to/copilot-demo/.github/ .

# Copy AGENTS.md (for Codex/autonomous agents)
cp /path/to/copilot-demo/AGENTS.md .

# Copy ExecPlan template
cp -r /path/to/copilot-demo/.agents/ .
```

**Step 2: Commit the files**
```bash
git add .github/ AGENTS.md .agents/
git commit -m "Add GitHub Copilot team configuration"
git push
```

**Step 3: Verify installation**
- `.github/copilot-instructions.md` → Auto-injected in all Copilot sessions
- `.github/prompts/*.prompt.md` → Available as commands
- `.github/agents/*.agent.md` → Available in VS Code agent mode
- `AGENTS.md` → Read by Codex for autonomous work
- `.agents/template/PLAN.md` → Template for complex features

---

## Using Prompts in VS Code

### What Are Prompts?

**Prompts** = Saved, reusable instructions that generate code in the chat window. You review the code, then manually create/edit files.

### Step-by-Step: Invoke a Prompt

#### Method 1: Slash Command (Easiest)

1. **Open relevant file** (e.g., `UserServiceImpl.java`)
2. **Open Copilot Chat** 
   - Menu: View → Command Palette → "Chat: Focus on Chat View"
   - Shortcut: `Cmd+Shift+I` (Mac) or `Ctrl+Shift+I` (Windows/Linux)
3. **Type slash + prompt name**: `/write-tests`
4. **Press Enter**
5. **Review generated code** in chat
6. **Click "Insert at Cursor"** or copy manually

**Example:**
```
[Open UserServiceImpl.java]
Copilot Chat: /write-tests
→ Generates complete test class
→ Click "Insert at Cursor"
→ Creates UserServiceImplTest.java
```

#### Method 2: Natural Language (Automatic)

When you reference a prompt by name in natural language, Copilot may automatically use it.

```
Copilot Chat: "Write tests for this service following team standards"
→ Copilot recognizes and uses write-tests.prompt.md
```

### Available Prompts & When to Use

| Prompt | Command | When to Use | Opens File |
|--------|---------|-------------|------------|
| **write-tests** | `/write-tests` | Need unit tests for service class | Service impl |
| **add-endpoint** | `/add-endpoint` | Add REST endpoint to controller | Controller |
| **explain-plan** | `/explain-plan` | Need architecture overview | Any |
| **review-diff** | `/review-diff` | Review code before commit | Any |
| **jira-story-to-code** | `/jira-story-to-code` | Convert story to implementation | None |

### VS Code Context Variables

Enhance prompts with context:

```
/write-tests #file
→ Uses current file as context

/review-diff #changes
→ Uses git staged changes

/explain-plan #codebase
→ Uses entire codebase context
```

---

## Using Prompts in IntelliJ

### Method 1: Manual Paste (Works Always)

1. **Open the prompt file**: `.github/prompts/write-tests.prompt.md`
2. **Copy the prompt content** (skip YAML front matter if present)
3. **Open relevant file**: `UserServiceImpl.java`
4. **Open Copilot Chat**
   - Right-click → "GitHub Copilot" → "Open Chat"
   - Or Tools → GitHub Copilot Chat
5. **Paste prompt** into chat
6. **Add context if needed**: "Use current file"
7. **Press Enter**
8. **Review and accept** generated code

**Example:**
```
1. Open .github/prompts/write-tests.prompt.md
2. Copy everything below the --- line
3. Open UserServiceImpl.java
4. GitHub Copilot Chat → paste prompt
5. Add: "Use the current file UserServiceImpl.java"
6. Review generated test
7. Accept or modify
```

### Method 2: YAML Front Matter (IntelliJ 2024.2+)

If your prompts have YAML front matter, IntelliJ can auto-discover them.

**Requirements:**
- IntelliJ IDEA 2024.2 or later
- GitHub Copilot plugin updated
- Prompts with YAML front matter

**How to use:**
1. **Open relevant file**: `UserServiceImpl.java`
2. **Right-click in editor**
3. **Select "AI Actions"** or "GitHub Copilot Actions"
4. **Choose prompt from menu**: "Write Tests (Enhanced)"
5. **IntelliJ auto-sends file context**
6. **Review generated code**

**Note**: Not all IntelliJ versions support this yet. If you don't see "AI Actions", use Method 1.

### IntelliJ Context Variables

```
#file - Current open file
#selection - Selected text
#codebase - Project files (limited context)
```

Add manually:
```
[Paste prompt]
Context: Use #file (UserServiceImpl.java)
```

---

## Using Agents in VS Code

### What Are Agents?

**Agents** = Autonomous workers that create files, run commands, and self-correct. You approve the plan once; the agent does everything else.

### Prerequisites

- ✅ VS Code (required)
- ✅ GitHub Copilot Chat extension
- ✅ Agent mode enabled
- ✅ Terminal access for commands (`./gradlew`, `git`, etc.)

### Step-by-Step: Using an Agent

#### 1. Enable Agent Mode

**Option A: Via Command Palette**
```
View → Command Palette → "Chat: Focus on Chat View"
Click the agent icon (🤖) or type "@"
```

**Option B: In Chat Window**
```
Open Copilot Chat
Type "@" to see available agents
Select agent from dropdown
```

#### 2. Select Agent

```
Copilot Chat: @workspace
Available agents:
- feature-builder
- pre-commit-review
- test-runner
- code-auditor
```

#### 3. Describe Task

```
@workspace /feature-builder
Build a product catalog feature. Products have:
- name, description, price, category, stock
- Admins can create/update/delete
- Everyone can view and search
```

#### 4. Review Plan

Agent will:
- Read your existing codebase
- Analyze patterns (entities, services, controllers)
- Show architecture plan
- List files to create
- Wait for your approval

```
## Architecture Plan

Files to Create:
- ProductEntity.java
- ProductRepository.java
- CreateProductRequest.java
- ProductResponse.java
- ProductService.java
- ProductServiceImpl.java
- ProductController.java
- ProductServiceImplTest.java

Things to Account For:
- Admin-only create/update (@PreAuthorize)
- Price validation (must be positive)
- Stock quantity tracking

Proceed? (yes/no)
```

#### 5. Approve Execution

```
You: yes
```

#### 6. Agent Executes

Agent will autonomously:
1. ✅ Create all entity files
2. ✅ Create repository
3. ✅ Create DTOs
4. ✅ Create service interface + impl
5. ✅ Create controller
6. ✅ Create tests
7. ✅ Run `./gradlew compileJava`
8. ✅ Fix any compilation errors
9. ✅ Re-compile until clean
10. ✅ Report summary

#### 7. Review Results

```
## Generation Complete

Created Files:
- src/main/java/.../model/ProductEntity.java
- src/main/java/.../repository/ProductRepository.java
- [... 6 more files]

Endpoints Available:
- POST   /api/v1/products      (Admin)
- GET    /api/v1/products      (All)
- GET    /api/v1/products/{id} (All)
- PUT    /api/v1/products/{id} (Admin)
- DELETE /api/v1/products/{id} (Admin)

Compilation: ✅ Success

Next Steps:
- Review generated code
- Run tests: ./gradlew test
- Test endpoints manually
```

### Available Agents

| Agent | Use Case | Creates Files | Runs Commands |
|-------|----------|---------------|---------------|
| **feature-builder** | Build complete feature | ✅ Yes (8-10) | ✅ Compile |
| **pre-commit-review** | Code review + test | ❌ No | ✅ Test, git |
| **test-runner** | Run tests, fix failures | ✅ Yes (fixes) | ✅ Test |
| **code-auditor** | Standards audit | ❌ No | ✅ Compile, test |

---

## Agents in IntelliJ (Alternatives)

### Why Agents Don't Work in IntelliJ

**Technical limitations:**
- ❌ No file system write access for AI
- ❌ No terminal command execution
- ❌ No multi-turn autonomous mode
- ❌ Copilot agent mode not supported

### Alternative: Manual Multi-Prompt Workflow

Simulate agent behavior using multiple prompts:

#### Simulating feature-builder Agent

**Step 1: Get Architecture Plan**
```
1. Copilot Chat (IntelliJ)
2. Paste /explain-plan prompt
3. Describe: "Product catalog feature..."
4. Review plan output
```

**Step 2: Create Entity**
```
1. Create new file: ProductEntity.java
2. Copilot Chat: "Create JPA entity for Product with fields: name, description, price, category, stock"
3. Accept generated code
```

**Step 3: Create Repository**
```
1. Create: ProductRepository.java
2. Copilot Chat: "Create Spring Data JPA repository for ProductEntity"
3. Accept
```

**Step 4: Create DTOs**
```
1. Create: CreateProductRequest.java
2. Copilot Chat: "Create request DTO Record with validation"
3. Create: ProductResponse.java
4. Copilot Chat: "Create response DTO Record"
```

**Step 5: Create Service**
```
1. Paste /add-endpoint prompt (modified for service)
2. Or use Copilot inline suggestions
3. Create interface + impl
```

**Step 6: Create Controller**
```
1. Paste /add-endpoint prompt
2. Generate all CRUD endpoints
```

**Step 7: Generate Tests**
```
1. Open ProductServiceImpl.java
2. Paste /write-tests prompt
3. Accept generated tests
```

**Step 8: Compile & Fix**
```
1. IntelliJ → Build → Build Project
2. Fix any errors manually
3. Rebuild
```

**Comparison:**
- **VS Code Agent**: 5 minutes, hands-off
- **IntelliJ Manual**: 30-45 minutes, guided but manual

---

## Working with ExecPlans

### What Is an ExecPlan?

**ExecPlan** = Structured markdown plan for complex features that acts as both:
1. **Design document** - Architecture decisions documented upfront
2. **Progress tracker** - Checkboxes for milestones
3. **Resume point** - Can pause and restart from plan alone

### When to Use ExecPlans

Create an ExecPlan when:
- ✅ Feature touches 5+ files
- ✅ Work will take multiple hours/days
- ✅ Multiple developers involved
- ✅ Need to pause and resume work
- ✅ Complex cross-cutting changes

**Don't use for:**
- ❌ Single file changes
- ❌ Bug fixes
- ❌ Simple refactors
- ❌ Quick tasks (< 30 min)

### How to Create an ExecPlan

#### Method 1: Via Agent (VS Code)

The agent automatically creates an ExecPlan for complex tasks.

```
Copilot Chat: @workspace
"Build a multi-tenant system with tenant isolation at database level"

Agent recognizes complexity → creates ExecPlan automatically
Location: .agents/multi-tenant-system/PLAN.md
```

#### Method 2: Manual Creation

```bash
# Copy template
cp .agents/template/PLAN.md .agents/notification-system/PLAN.md

# Edit the plan
code .agents/notification-system/PLAN.md
```

### ExecPlan Structure

```markdown
# ExecPlan: Notification System

## Overview
**Task**: Email/SMS notifications for contract expiry
**Type**: [x] New Feature
**Estimated Complexity**: [x] Complex (1+ days)

## Context
Existing:
- ContractEntity already tracks expiry dates
- Email service exists (EmailService.java)

New:
- Need scheduled job to check daily
- Need notification preferences per user
- Need SMS provider integration

## Requirements
Must:
- Daily check for contracts expiring within 30 days
- Send email to admin
- Track notifications sent (no duplicates)

Must NOT:
- Send notification more than once per contract
- Block main application (async)

## Architecture Decisions
| Decision | Chosen | Alternatives | Reason |
|----------|--------|--------------|--------|
| Scheduler | @Scheduled (Spring) | Quartz | Simpler for single job |
| Notification tracking | New table | Cache | Permanent record needed |
| Async | @Async | Kafka | Simpler, sufficient |

## Files Impact

### Files to Create
- NotificationEntity.java - Track sent notifications
- NotificationRepository.java
- ContractExpiryScheduler.java - @Scheduled daily job
- NotificationService.java - Send logic
- ExpiryEmailTemplate.html - Email template

### Files to Modify
- application.yml - Add scheduler config
- SecurityConfig.java - Allow scheduler context

## Milestones
- [ ] Milestone 1 — Research (read existing ContractEntity, EmailService)
- [ ] Milestone 2 — Create NotificationEntity + Repo
- [ ] Milestone 3 — Create NotificationService
- [ ] Milestone 4 — Create Scheduler
- [ ] Milestone 5 — Create Email Template
- [ ] Milestone 6 — Tests
- [ ] Milestone 7 — Compile and Test
- [ ] Milestone 8 — Manual test with real dates

## Progress Log
| Timestamp | Milestone | Status | Notes |
|-----------|-----------|--------|-------|
| 2025-01-15 10:00 | Research | ✅ Done | Found EmailService.sendEmail() method |
| 2025-01-15 10:30 | Data Layer | 🔄 In Progress | Entity created, repo pending |

## Decisions Log
| Decision | Reason |
|----------|--------|
| Added sentAt timestamp | Track when notification was sent for auditing |

## Restart Instructions
If resuming from scratch:
1. Read this entire PLAN.md
2. Check Progress Log - currently at Milestone 2
3. Read NotificationEntity.java (already created)
4. Continue with NotificationRepository.java
```

### Using ExecPlans: Complete Workflow

#### VS Code (Automatic with Agent)

```
Step 1: Describe Complex Feature
Copilot Chat: @workspace
"Build notification system for contract expiry with email alerts"

Step 2: Agent Creates ExecPlan
Agent: "This is complex. Creating ExecPlan..."
Location: .agents/notification-system/PLAN.md

Step 3: Review Plan
[Opens PLAN.md automatically]
Review: architecture, files, milestones

Step 4: Approve or Request Changes
You: "Change scheduler to run every 12 hours instead of daily"
Agent: [Updates plan]
You: "Looks good, proceed"

Step 5: Agent Executes Milestone by Milestone
✅ Milestone 1 - Research
✅ Milestone 2 - Entity + Repo
🔄 Milestone 3 - Service (in progress)
[ ] Milestone 4 - Scheduler
[ ] Milestone 5 - Template
[ ] Milestone 6 - Tests

[Can pause here - everything saved in PLAN.md]

Step 6: Resume Later (Next Day)
Copilot Chat: "Resume the notification system plan"
Agent: [Reads .agents/notification-system/PLAN.md]
Agent: "Last status: Milestone 3 in progress. Continuing..."
[Picks up exactly where it left off]

Step 7: Completion
Agent marks all milestones ✅
Agent updates Progress Log with final timestamp
PLAN.md remains as permanent record
```

#### IntelliJ (Manual with Plan)

```
Step 1: Create Plan Manually
cp .agents/template/PLAN.md .agents/notification-system/PLAN.md
[Edit in IntelliJ, fill all sections]

Step 2: Execute Milestone 1 (Research)
[Manually read ContractEntity, EmailService]
[Update Progress Log: ✅ Milestone 1 complete]

Step 3: Execute Milestone 2 (Entity + Repo)
Use prompts to generate:
- Copilot Chat: Generate NotificationEntity
- Copilot Chat: Generate NotificationRepository
[Update Progress Log: ✅ Milestone 2 complete]

Step 4: Execute Milestone 3 (Service)
[Generate NotificationService using prompts]
[Update Progress Log: ✅ Milestone 3 complete]

[Continue for each milestone]

Step 5: Update Plan Throughout
After each milestone: edit PLAN.md
- Check off completed milestone
- Add timestamp to Progress Log
- Note any deviations in Decisions Log

Step 6: Resume Next Session
Open PLAN.md → read Progress Log → continue from next unchecked milestone
```

### ExecPlan Best Practices

1. **Always read existing code first** before filling the plan
2. **Be specific in Architecture Decisions** - document alternatives
3. **Update Progress Log after every stop** - even if just 10 minutes
4. **Log deviations in Decisions Log** - "Why did we do this differently?"
5. **Don't delete the plan when done** - it's permanent documentation

---

## Complete Workflow Examples

### Example 1: Generate Tests (Simple Task)

#### VS Code
```
1. Open UserServiceImpl.java
2. Copilot Chat: /write-tests
3. Review generated test
4. Click "Insert at Cursor"
5. Done in 30 seconds
```

#### IntelliJ
```
1. Open UserServiceImpl.java
2. Open .github/prompts/write-tests.prompt.md
3. Copy prompt content
4. Copilot Chat → paste
5. Add: "Use current file"
6. Review and accept
7. Done in 1 minute
```

---

### Example 2: Add Single Endpoint (Medium Task)

#### VS Code
```
1. Open ProductController.java
2. Copilot Chat: /add-endpoint
   "Add PATCH /products/{id}/price to update price only"
3. Copilot generates:
   - UpdatePriceRequest.java (new file)
   - Updates ProductService interface
   - Updates ProductServiceImpl
   - Updates ProductController
4. Review all changes
5. Accept
6. Done in 2 minutes
```

#### IntelliJ
```
1. Open ProductController.java
2. Paste /add-endpoint prompt
3. Specify: "PATCH /products/{id}/price"
4. Manually create UpdatePriceRequest.java from output
5. Manually update ProductService interface
6. Manually update ProductServiceImpl
7. Manually update ProductController
8. Done in 5-10 minutes
```

---

### Example 3: Build Complete Feature (Complex Task)

#### VS Code with Agent
```
1. Copilot Chat: @workspace feature-builder
   "Build order management system. Orders have:
   - Multiple line items (products + quantities)
   - Status workflow: DRAFT → SUBMITTED → APPROVED → SHIPPED
   - Only order owner can view
   - Admins can approve/reject"

2. Agent reads existing code (products, users, patterns)

3. Agent shows architecture plan:
   - OrderEntity (with status enum)
   - OrderItemEntity (many-to-one with Order)
   - OrderRepository with custom queries
   - 4 DTOs (CreateOrderRequest, OrderItemRequest, OrderResponse, etc.)
   - OrderService with state machine logic
   - OrderController with role checks
   - 12 test scenarios

4. You: "Looks good, proceed"

5. Agent creates all 15 files

6. Agent runs ./gradlew compileJava → Success ✅

7. Agent runs ./gradlew test → 2 failures

8. Agent reads failure stack traces

9. Agent fixes both issues

10. Agent reruns tests → All pass ✅

11. Done in 5-7 minutes, fully working feature
```

#### IntelliJ Manual
```
1. Copilot Chat: /explain-plan (paste prompt)
   [Get architecture overview]

2. Create OrderEntity.java manually
   Use Copilot inline suggestions

3. Create OrderItemEntity.java

4. Create OrderRepository with custom queries
   Copilot Chat: "Create repository with findByUserId and findByStatus"

5. Create 4 DTOs using Record format
   Copilot inline helps

6. Create OrderService interface + impl
   Complex state machine logic
   Use Copilot for boilerplate

7. Create OrderController
   Paste /add-endpoint for each endpoint (5 times)

8. Copilot Chat: /write-tests (paste)
   Generate test suite

9. Build Project → Fix compilation errors

10. Run tests → Fix failing tests

11. Done in 45-60 minutes
```

---

### Example 4: Pre-Commit Code Review

#### VS Code with Agent
```
1. Make changes to code
2. git add src/main/java/com/company/user/
3. Copilot Chat: @workspace pre-commit-review
4. Agent reads staged diff
5. Agent reviews against standards
6. Agent identifies affected tests
7. Agent runs: ./gradlew test --tests "UserServiceImplTest"
8. Agent reports:
   ❌ BLOCKED
   - Missing @Valid on controller
   - Test failure: duplicate email scenario
9. Fix issues
10. git add .
11. Repeat pre-commit-review
12. ✅ APPROVED
13. git commit -m "Add user validation"
```

#### IntelliJ Manual
```
1. Make changes
2. git add files
3. Terminal: git diff --staged > /tmp/diff.txt
4. Copy diff content
5. Paste /review-diff prompt
6. Add diff content
7. Review feedback
8. Terminal: ./gradlew test
9. Read test output manually
10. Fix any issues
11. Rebuild and retest
12. git commit
```

---

## Troubleshooting

### VS Code Issues

**Problem**: Slash commands not working
```
Solution:
- Ensure GitHub Copilot Chat extension installed
- Restart VS Code
- Check: View → Command Palette → "Reload Window"
```

**Problem**: Agent mode not available
```
Solution:
- Update GitHub Copilot extension to latest
- Check subscription includes Copilot Chat
- VS Code version 1.80+ required
```

**Problem**: Agent can't create files
```
Solution:
- Check file permissions in workspace
- Ensure VS Code has write access
- Try: File → Save Workspace As
```

**Problem**: ./gradlew commands fail
```
Solution:
- Ensure Gradle installed: ./gradlew --version
- Check Java version: java -version
- Verify in root of Spring Boot project
```

### IntelliJ Issues

**Problem**: Prompts not showing in AI Actions menu
```
Solution:
- Check IntelliJ version 2024.2+
- Update GitHub Copilot plugin
- Verify .github/prompts/ exists in project
- Check YAML front matter syntax
```

**Problem**: Copilot not reading context
```
Solution:
- Explicitly add: "Use #file UserService.java"
- Select code before invoking
- Include file content in prompt manually
```

**Problem**: Generated code doesn't match project style
```
Solution:
- Ensure .github/copilot-instructions.md exists
- Restart IDE after adding
- First prompt: "Read copilot-instructions.md"
```

### General Issues

**Problem**: Copilot generates wrong patterns
```
Solution:
- Verify copilot-instructions.md is committed
- GitHub indexes it after commit + push
- Wait 5-10 minutes after first commit
- Test: "What Java version should I use?" (should say 21)
```

**Problem**: ExecPlan not being recognized
```
Solution:
- Ensure AGENTS.md at project root
- Explicitly say: "Create an ExecPlan for..."
- Use Codex model (cdex 5.2) if available
```

---

## Quick Reference

### VS Code Commands
| Task | Command |
|------|---------|
| Open Chat | `Cmd/Ctrl + Shift + I` |
| Invoke Prompt | `/prompt-name` |
| Agent Mode | `@workspace` |
| Add Context | `#file`, `#selection`, `#changes` |

### IntelliJ Commands
| Task | Action |
|------|--------|
| Open Chat | Right-click → GitHub Copilot → Open Chat |
| Use Prompt | Paste from `.github/prompts/` |
| Add Context | Manually: "Use #file FileName.java" |

### File Locations
```
.github/
├── copilot-instructions.md   (auto-injected)
├── prompts/
│   ├── write-tests.prompt.md
│   ├── add-endpoint.prompt.md
│   ├── explain-plan.prompt.md
│   ├── review-diff.prompt.md
│   └── jira-story-to-code.prompt.md
└── agents/
    ├── feature-builder.agent.md
    ├── pre-commit-review.agent.md
    ├── test-runner.agent.md
    └── code-auditor.agent.md

AGENTS.md                      (root - for Codex/agents)
.agents/
└── template/
    └── PLAN.md                (ExecPlan template)
```

---

## Summary

| Feature | VS Code | IntelliJ | Effort |
|---------|---------|----------|--------|
| **Prompts** | ✅ Slash commands | ⚠️ Manual paste | Low |
| **Agents** | ✅ Full support | ❌ Not available | High |
| **ExecPlans** | ✅ Auto-generated | ⚠️ Manual creation | Medium |
| **Auto-context** | ✅ #file, #changes | ⚠️ Manual | Low |
| **File creation** | ✅ Autonomous | ❌ Manual | High |
| **Terminal execution** | ✅ Yes | ❌ No | N/A |

**Recommendation**: 
- Use **VS Code** for complex features with agents
- Use **IntelliJ** for daily development with prompts
- Keep both IDEs available and switch based on task complexity
