# Understanding Agents vs Prompts vs Skills

A clear guide to understanding the different AI instruction patterns and how they work.

---

## The Core Difference: Control & Autonomy

Think of it like hiring help:

| Type | Analogy | Control | Autonomy | Example |
|------|---------|---------|----------|---------|
| **Prompt** | Recipe | You follow each step | None | "Add flour, stir, bake" |
| **Agent** | Chef | You set the goal | High | "Make me dinner" |
| **Skill** | Kitchen Tool | You use it when needed | None | "Use the blender" |

---

## 1. Prompts (`.prompt.md` files)

### What They Are
**Saved instructions** that you invoke manually. They run once, do exactly what the instructions say, and stop. You're still in control at every step.

### Characteristics
- ✅ **Manual invocation** - You trigger them
- ✅ **Single execution** - Runs once and stops
- ✅ **Predictable** - Same input → same type of output
- ✅ **You're in control** - You review and decide next steps
- ✅ **No file system access** - Only generates code in chat
- ✅ **No terminal execution** - Cannot run commands

### Example: `write-tests.prompt.md`

```markdown
# Write Tests Prompt

Generate a complete test suite for the Java service class provided.

### What to generate
- Unit Test Class using @ExtendWith(MockitoExtension.class)
- Test every public method
- Cover: happy path, null inputs, exceptions
- Use AAA pattern
...
```

**How it works:**
```
You: [Open UserService.java] → Copilot Chat → /write-tests
Copilot: [Generates test class code in chat]
You: [Review the code]
You: [Copy and create the file OR ask for changes]
```

**Key point**: Copilot shows you the code. **YOU** decide whether to create the file.

---

## 2. Agents (`.agent.md` files)

### What They Are
**Autonomous workers** that you give a goal to. They plan, execute multiple steps, read your codebase, create/modify files, run terminal commands, and report back. They make decisions along the way.

### Characteristics
- ✅ **Goal-oriented** - You describe what, not how
- ✅ **Multi-step execution** - Performs many actions autonomously
- ✅ **File system access** - Creates and modifies files directly
- ✅ **Terminal execution** - Runs commands (compile, test, git, etc.)
- ✅ **Decision making** - Chooses approach based on codebase analysis
- ✅ **Self-correcting** - If compilation fails, fixes and retries

### Example: `feature-builder.agent.md`

```markdown
# Feature Builder Agent

You are a senior Java architect. Take a requirement and build 
a complete, production-ready Spring Boot feature.

### Step 1 — Understand the Requirement
1. Read existing project structure
2. Run: `find . -type f -name "*.java" | head -40`
3. Read one existing entity, service, controller
...

### Step 2 — Produce an Architecture Plan
[Creates structured plan]

### Step 3 — Generate All Files
[Creates 8-9 files]

### Step 4 — Compile Verification
[Runs `./gradlew compileJava`]
...
```

**How it works:**
```
You: [Agent mode] → feature-builder
     "Build user address management feature"

Agent: [Reads existing code files]
       [Analyzes patterns]
       [Shows architecture plan]
       "Proceed? (yes/no)"

You: "yes"

Agent: [Creates AddressEntity.java]
       [Creates AddressRepository.java]
       [Creates AddressService.java]
       [Creates AddressServiceImpl.java]
       [Creates DTOs]
       [Creates Controller]
       [Creates Tests]
       [Runs ./gradlew compileJava]
       [Reports results]
```

**Key point**: Agent does the work. **You** approve the plan upfront.

---

## 3. Skills (Claude/Anthropic Concept)

### What They Are
**Pre-trained capabilities** that Claude can use when needed. Think of them as built-in functions that Claude knows how to use automatically when the situation calls for it.

### Characteristics
- ✅ **Auto-invoked** - Claude uses them when relevant
- ✅ **Part of the model** - Built into Claude's training
- ✅ **Composable** - Can be combined with other skills
- ✅ **Context-aware** - Used based on conversation context

### Examples of Claude Skills
- **Code explanation** - Breaking down complex code
- **Bash scripting** - Generating shell scripts
- **LaTeX** - Writing mathematical formulas
- **Debugging** - Analyzing errors systematically
- **Refactoring** - Improving code structure

### How Skills Work

You don't invoke them explicitly. Claude recognizes the task and applies relevant skills.

```
You: "Explain how this Spring Security filter chain works"

Claude: [Uses code explanation skill]
        [Breaks down in steps]
        [Explains security flow]
        [Identifies patterns]
```

**Key point**: Skills are **invisible helpers** Claude uses automatically.

---

## Detailed Comparison Table

| Aspect | Prompt | Agent | Skill |
|--------|--------|-------|-------|
| **Invocation** | Manual (`/prompt-name`) | Manual (agent mode) | Automatic |
| **Control Level** | You control each step | You approve plan, agent executes | Claude decides |
| **File Creation** | No (shows code only) | Yes (direct file system access) | No |
| **Terminal Access** | No | Yes (can run commands) | No |
| **Multi-step** | Single action | Multiple autonomous steps | Per context |
| **Decision Making** | None (follows script) | Yes (chooses approach) | Yes (applies when relevant) |
| **Requires Approval** | Every output | Once at plan stage | Never |
| **Best For** | Repetitive code generation | Complex multi-file features | Natural conversation |
| **VS Code Support** | ✅ Yes | ✅ Yes | N/A (model feature) |
| **IntelliJ Support** | ⚠️ Manual paste | ❌ No | N/A (model feature) |

---

## Front Matter: YAML Convention

### What Is Front Matter?

**Definition**: Structured metadata at the top of markdown files, enclosed in triple dashes (`---`). Originally from static site generators (Jekyll, Hugo), now adopted by modern AI tools.

**Format:**
```yaml
---
key: "value"
list: ["item1", "item2"]
number: 42
---
```

### Why Front Matter Exists

Without front matter, tools must:
- ❌ Parse the entire file to understand what it does
- ❌ Rely on filename conventions
- ❌ Can't easily filter, search, or organize prompts
- ❌ No version control or metadata tracking

With front matter, tools can:
- ✅ Read metadata in milliseconds (first few lines only)
- ✅ Show prompt library UI with descriptions
- ✅ Filter by tags, model, version
- ✅ Know which context is required
- ✅ Track versions and authors

### YAML Front Matter Example

```markdown
---
name: "Write Tests"
description: "Generate complete test suite for service class"
model: "gpt-4.1"
context: ["file"]
tags: ["testing", "junit", "mockito"]
version: "1.0.0"
author: "Java Team"
requires_approval: false
---

# Write Tests Prompt

[Rest of the content...]
```

---

## Advantages of Front Matter

### 1. **IDE Integration** ⭐⭐⭐⭐⭐
**The #1 reason to use it**

**Without front matter:**
```
IntelliJ: Shows filename only
"write-tests.prompt.md"
You click it → reads entire file → extracts instructions → sends to AI
```

**With front matter:**
```
IntelliJ AI Actions menu shows:
📝 Write Tests
   "Generate complete test suite for service class"
   Model: GPT-4.1 | Context: File | Version: 1.0.0

One click → IntelliJ knows:
  - Which model to use (gpt-4.1)
  - What context to send (#file - current open file)
  - What it does (from description)
```

**Benefit**: Faster, better UX, no need to open file to understand it.

---

### 2. **Discoverability & Organization** ⭐⭐⭐⭐
**Makes prompt libraries actually usable**

With 20+ prompts in a folder:
- ✅ Search by tag: "Show me all testing prompts"
- ✅ Filter by model: "Show prompts that use Claude Sonnet"
- ✅ Sort by version: "Show me v2.0+ prompts"
- ✅ Group by context: "Show prompts that need #codebase"

**Example - IntelliJ can build this UI:**
```
Testing Prompts (5)
├─ Write Tests (v1.0.0) - GPT-4.1
├─ Write Integration Tests (v1.2.0) - Claude Sonnet
└─ Generate Test Data (v1.0.0) - Claude Haiku

Refactoring Prompts (3)
├─ Extract Service (v2.0.0) - GPT-4.1
└─ Rename Consistently (v1.0.0) - Claude Haiku
```

Without front matter, just a flat list of filenames.

---

### 3. **Version Control & Tracking** ⭐⭐⭐⭐
**Know what version of prompt is being used**

```yaml
---
version: "2.1.0"
changelog: "Added support for Spring Boot 3.2 test patterns"
author: "Senior Dev"
last_updated: "2025-01-15"
---
```

**Benefits:**
- Team knows which version they're using
- Can track improvements over time
- Can deprecate old versions
- Can require minimum version for certain features

**Example:**
```
Team member: "This test prompt isn't working right"
Senior dev: "Which version?"
Team member: "1.0.0"
Senior dev: "Update to 2.1.0, we fixed that in December"
```

---

### 4. **Model Specification** ⭐⭐⭐⭐
**Automatically use the right AI model**

```yaml
---
model: "claude-sonnet-4"
fallback_model: "gpt-4.1"
---
```

**Without front matter:**
```
Developer manually selects model
Wrong model = poor results
Have to remember which prompt needs which model
```

**With front matter:**
```
IntelliJ reads model: "claude-sonnet-4"
Automatically routes to that model
Consistent results across team
```

---

### 5. **Context Requirements** ⭐⭐⭐⭐⭐
**Critical for correct prompt execution**

```yaml
---
context: ["file", "selection", "codebase"]
requires: "Service implementation class must be open"
---
```

**Why this matters:**

**Scenario**: "Add Endpoint" prompt
```yaml
context: ["file"]  # Need current controller file
```

**Without front matter:**
- Developer invokes prompt
- Forgets to open controller file
- Copilot generates generic code (no context)
- Waste of time

**With front matter:**
- IntelliJ checks: Is current file open? ✅
- IntelliJ checks: Is it a Controller class? ✅
- IntelliJ auto-sends current file as context
- Copilot generates code that fits THIS controller

---

### 6. **Machine-Readable for Tooling** ⭐⭐⭐
**Build automation around prompts**

```yaml
---
tags: ["pre-commit", "automated"]
trigger: "on_staged_changes"
---
```

**Example use case**: Custom pre-commit hook
```bash
# Script reads all prompts with tag "pre-commit"
# Automatically runs them on git pre-commit
git add .
# Script finds: review-diff.prompt.md (tagged "pre-commit")
# Runs automatically → blocks commit if issues found
```

You can build:
- IDE extensions that show prompt stats
- CLI tools that run prompts in CI/CD
- Prompt version managers
- Prompt testing frameworks

---

## Which Files Should Have Front Matter?

### ✅ **PROMPTS** (`.prompt.md`) - **HIGHLY RECOMMENDED**

**Why**: Maximum benefit
- IntelliJ integration relies on it
- Enables UI discovery
- Model specification critical
- Context requirements prevent mistakes

**Example:**
```yaml
---
name: "Add REST Endpoint"
description: "Add new endpoint to existing controller"
model: "gpt-4.1"
context: ["file", "selection"]
tags: ["api", "controller", "rest"]
version: "1.0.0"
---
```

**Impact**: 10x better UX in IntelliJ, discoverable in VS Code tools too

---

### ⚠️ **AGENTS** (`.agent.md`) - **OPTIONAL BUT USEFUL**

**Why**: Less critical (agents only work in VS Code currently)
- VS Code agent picker doesn't use front matter yet
- But future-proofing for when it does
- Helps with documentation and organization
- Good practice for consistency

**Example:**
```yaml
---
name: "Feature Builder"
description: "End-to-end feature scaffolding with compilation"
model: "claude-sonnet-4"
requires: ["gradle", "git"]
execution_mode: "autonomous"
version: "1.0.0"
---
```

**Impact**: No immediate benefit, but prepares for future IDE improvements

---

### ❌ **AGENTS.md** (Root instruction file) - **NOT APPLICABLE**

**Why**: It's a documentation file that AI reads, not a prompt
- Not invoked by user
- No UI to show metadata
- AI reads entire file anyway
- Front matter would be ignored

**Keep as**: Plain markdown

---

### ❌ **copilot-instructions.md** - **NOT APPLICABLE**

**Why**: Auto-injected by GitHub Copilot
- GitHub's system doesn't look for front matter here
- Always injected fully into every session
- No need for metadata (there's only one per repo)

**Keep as**: Plain markdown

---

### ✨ **PLAN.md** (ExecPlan template) - **USEFUL BUT NOT CRITICAL**

**Why**: Could help with tooling
- Scripts that parse plan status
- Tools that track milestone completion
- Version tracking of plan template

**Example:**
```yaml
---
template_version: "1.0.0"
plan_type: "feature"
schema_version: "1.0"
---
```

**Impact**: Helpful if building automation around plans

---

### ❌ **Regular Docs** (README.md, guides) - **NOT NEEDED**

**Why**: These are human-readable documentation
- Not invoked as prompts
- No IDE integration needed
- Standard markdown is clearer

**Keep as**: Plain markdown

---

## Front Matter Field Reference

### Standard Fields

| Field | Type | Purpose | Example |
|-------|------|---------|---------|
| `name` | string | Display name in UI | `"Write Tests"` |
| `description` | string | Short explanation | `"Generate JUnit test suite"` |
| `model` | string | Preferred AI model | `"gpt-4.1"` |
| `context` | array | Required context | `["file", "selection"]` |
| `tags` | array | Categories/filters | `["testing", "junit"]` |
| `version` | string | Semantic version | `"1.0.0"` |
| `author` | string | Creator | `"Java Team"` |

### Advanced Fields

| Field | Type | Purpose | Example |
|-------|------|---------|---------|
| `fallback_model` | string | Backup model | `"gpt-4.1"` |
| `requires` | array | Dependencies | `["gradle", "git"]` |
| `ide_support` | array | Compatible IDEs | `["vscode", "intellij"]` |
| `execution_mode` | string | How it runs | `"interactive"` or `"autonomous"` |
| `changelog` | string | Recent changes | `"Added Spring Boot 3.2 support"` |
| `last_updated` | date | Update timestamp | `"2025-01-15"` |
| `requires_approval` | boolean | Needs user OK | `true` |

---

## IntelliJ Prompt Support (2024+)

IntelliJ now fully supports **Prompt Files** with YAML front matter:

**Features:**
- ✅ Auto-discovery of `.prompt.md` in `.github/prompts/`
- ✅ Shows in "AI Actions" context menu
- ✅ Displays name + description from front matter
- ✅ Auto-routes to specified model
- ✅ Context variable support (`#file`, `#selection`, `#codebase`)
- ✅ Keyboard shortcuts for favorites
- ✅ Recent prompts history

**Usage:**
```markdown
---
name: "Generate Tests"
model: "gpt-4"
context: ["file"]
---

Generate JUnit tests for the current class...
```

1. Right-click in editor
2. "AI Actions" → "Generate Tests"
3. IntelliJ sends current file + prompt to GPT-4
4. Results appear

**However**: IntelliJ still **does NOT support agents** (autonomous execution).

---

## Should You Use YAML Front Matter?

### ✅ Add Front Matter If:
- You're using IntelliJ (better integration)
- You have many prompts (helps organization)
- You want version tracking
- You're building tooling around prompts

### ⚠️ Can Skip If:
- Only using VS Code (works fine without)
- Small number of prompts (< 10)
- Quick personal project

### Example Conversion

**Before (current style):**
```markdown
# Write Tests Prompt

> **How to use in VS Code**: Open the service class, type `/write-tests`
> **Model**: GPT-4.1 or Claude Haiku

## Prompt
Generate a complete test suite...
```

**After (with YAML front matter):**
```markdown
---
name: "Write Tests"
description: "Generate complete test suite for service class"
model: "gpt-4.1"
context: ["file"]
tags: ["testing", "junit", "mockito"]
version: "1.0.0"
author: "Team"
---

# Write Tests

Generate a complete test suite for the Java service class provided.

### What to generate
- Unit Test Class using @ExtendWith(MockitoExtension.class)
...
```

---

## IntelliJ Integration Update

### What IntelliJ NOW Supports (2024+)

**✅ Prompt Files** with features:
- Auto-detection of `.prompt.md` in `.github/prompts/`
- Quick access from editor context menu
- Context variables: `#file`, `#selection`, `#codebase`
- Model specification in YAML front matter
- Keyboard shortcuts for invoking prompts

**Example IntelliJ prompt file:**
```markdown
---
name: "Add Endpoint"
model: "gpt-4-turbo"
context: ["file", "selection"]
---

Add a REST endpoint to the current controller class.

Use the selected text as the endpoint specification.
Follow team standards: @Valid, ResponseEntity, etc.
```

**Usage in IntelliJ:**
1. Right-click in editor
2. "AI Actions" → "Add Endpoint"
3. Select code if needed
4. Copilot generates with context

### What IntelliJ DOES NOT Support

**❌ Agent Files** - No autonomous execution:
- Cannot create files directly
- Cannot run terminal commands
- Cannot read other files autonomously
- Cannot self-correct based on compilation errors

**Why?** Agents require:
- Deep IDE integration for file system access
- Terminal execution capabilities
- Multi-turn autonomous operation

Currently, only VS Code + Copilot Agent Mode supports this.

---

## Practical Workflow Examples

### Scenario 1: Generate Tests (Simple Task)

**Using a Prompt** ✅ Best choice
```
IntelliJ or VS Code:
1. Open UserService.java
2. Invoke /write-tests prompt
3. Review generated test code
4. Create file
5. Done in 30 seconds
```

**Why not an agent?** Overkill - it's a single file generation task.

---

### Scenario 2: Build Complete Feature (Complex Task)

**Using an Agent** ✅ Best choice (if in VS Code)
```
VS Code Agent Mode:
1. Invoke feature-builder agent
2. Describe: "Build product catalog feature..."
3. Review architecture plan
4. Approve
5. Agent creates 8-9 files
6. Agent verifies compilation
7. Done in 5 minutes
```

**Using Prompts** ⚠️ Fallback (if in IntelliJ)
```
IntelliJ:
1. Use /explain-plan prompt → get architecture
2. Use /add-endpoint prompt → create controller
3. Manually create entity
4. Manually create repository
5. Use /write-tests prompt → create tests
6. Manually compile and fix errors
7. Done in 30-45 minutes
```

**Why agent is better here?** Handles all steps autonomously, self-corrects.

---

### Scenario 3: Code Review Before Commit

**Using an Agent** ✅ Best choice (if in VS Code)
```
git add .
Agent: pre-commit-review
→ Reviews code
→ Runs tests
→ Reports issues
```

**Using a Prompt** ⚠️ Alternative
```
git diff --staged > /tmp/diff.txt
Copy diff content
Paste into /review-diff prompt
→ Get review feedback manually
→ You run tests manually
```

---

## Recommendation for Your Repo

### ✅ Keep Current Structure
Your current setup is fine and works across IDEs:
- Clear documentation in comments
- Explicit instructions
- Works in both VS Code and IntelliJ

### ✨ Enhanced Version with YAML Front Matter

If you want better IntelliJ integration, add front matter:

**Example: `.github/prompts/write-tests.prompt.md`**
```markdown
---
name: "Write Tests"
description: "Generate complete JUnit 5 + Mockito test suite"
model: "gpt-4.1"
context: ["file"]
tags: ["testing", "junit", "mockito", "spring-boot"]
version: "1.0.0"
ide_support: ["vscode", "intellij"]
---

# Write Tests Prompt

> **Model**: GPT-4.1 or Claude Haiku
> **Context needed**: Open service class file

Generate a complete test suite for the Java service class provided.

### What to generate
[Rest of your current prompt...]
```

This way you get:
- ✅ Works in VS Code (as before)
- ✅ Better integration in IntelliJ (discoverable, metadata)
- ✅ Self-documenting (version, model, context requirements)

---

## Summary: The Key Differences

### Prompts = Instructions You Follow
- "Here's how to make a cake" (recipe)
- You do each step
- Predictable output

### Agents = Goals You Set
- "Make me dinner" (outcome)
- They figure out how
- Autonomous execution

### Skills = Hidden Abilities
- Like knowing how to use a knife
- Applied automatically when relevant
- Part of the AI's training

---

## Quick Decision Guide

**Use a PROMPT when:**
- ✅ Single file generation
- ✅ Predictable template-based output
- ✅ You want to review before creating files
- ✅ Working in IntelliJ

**Use an AGENT when:**
- ✅ Multi-file feature (5+ files)
- ✅ Need compilation verification
- ✅ Need test execution
- ✅ Complex cross-cutting changes
- ✅ Working in VS Code

**SKILLS are automatic** - just use Claude and they apply when relevant.

---

## Final Word on IntelliJ Support

**Current State (2024-2025):**
- ✅ Prompts work (with YAML front matter = better UX)
- ❌ Agents don't work (no autonomous execution)
- ✅ Copilot inline suggestions work
- ✅ Copilot Chat works
- ✅ Skills (in Claude) work

**For IntelliJ users**: This repo is still valuable - you get consistent code from prompts and `copilot-instructions.md`. You just invoke prompts manually instead of having autonomous agents.
