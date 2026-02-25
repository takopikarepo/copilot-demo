---
name: "Review Diff"
description: "Code review from git diff against team standards - pre-commit quality gate"
model: "claude-sonnet"
fallback_model: "gpt-4.1"
context: ["changes"]
tags: ["code-review", "quality", "standards", "pre-commit", "git"]
version: "1.0.0"
ide_support: ["vscode", "intellij"]
author: "Java Team"
---

# Review Diff Prompt

> **How to use in VS Code**: Stage your changes with `git add`, then in Copilot Chat type `/review-diff #changes`
> **How to use in IntelliJ**: Run `git diff --staged` in terminal, copy output, paste into Copilot Chat with this prompt
> **Model**: Claude Sonnet for thorough review

---

## Prompt

You are a senior Java developer performing a code review. Review the provided code changes (diff) against our team standards.

### Review Checklist

Work through each changed file and check:

**Architecture**
- [ ] Controller only handles HTTP — no business logic
- [ ] Service contains all business logic
- [ ] Repository only does data access
- [ ] Correct layer calls correct layer (no skipping)

**Code Quality**
- [ ] Constructor injection used — no `@Autowired` on fields
- [ ] DTOs are Java Records
- [ ] No null returns — `Optional` or exceptions used
- [ ] Exception handling is specific — no empty catch blocks
- [ ] No `System.out.println` — SLF4J used
- [ ] No hardcoded values (URLs, timeouts, credentials, magic numbers)

**Spring Boot**
- [ ] `@Transactional` on service write methods
- [ ] `@Valid` on controller `@RequestBody` params
- [ ] Entities not directly returned from controllers
- [ ] Config externalized to `application.yml`

**Security**
- [ ] No credentials or secrets in code
- [ ] User input is validated
- [ ] Authorization checks present where needed
- [ ] No sensitive data in log statements

**Tests**
- [ ] New public methods have corresponding tests
- [ ] Test naming follows `method_scenario_result` convention

### Output Format

```
## Code Review

### Overall Assessment
✅ Approved / ⚠️ Approved with comments / ❌ Changes requested

---

### Issues Found

#### ❌ Must Fix
- **[File:Line]** — [issue]
  > Suggestion: [how to fix]

#### ⚠️ Should Fix
- **[File:Line]** — [issue]
  > Suggestion: [how to fix]

#### 💡 Nice to Have
- **[File:Line]** — [suggestion]

---

### What Looks Good
- [positive observations — always include at least one]

---

### Summary
[2-3 sentence overall assessment]
```

### Rules for the Review
- Be specific — always reference the exact file and line number
- Explain WHY something is an issue, not just that it is one
- Suggest the fix — do not just flag the problem
- Balance criticism with acknowledgment of good practices
