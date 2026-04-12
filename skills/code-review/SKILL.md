---
name: code-review
description: Five-axis code review with Codex adversarial review for high-risk code. Use when reviewing code before merge.
---

# Code Review

## Overview

Review code across five axes: correctness, design, complexity, naming, and test coverage. For high-risk code, add a cross-model adversarial review via Codex. The goal is catching problems before they ship, not gatekeeping.

## When to Use

- Before merging any feature branch
- After completing implementation tasks
- When requested by user (`/review`)
- At integration checkpoints

## When NOT to Use

- During active implementation — finish the task first
- For exploratory/throwaway code

## Integration Points

**Codex:** For high-risk code (auth, payments, concurrency, caching, public APIs, data migrations), run adversarial review:
```
/codex:adversarial-review --background [specific concern]
```
For full branch review before merge:
```
/codex:review --base main --background
```

**Entire:** The review happens at a checkpoint boundary. If the review finds issues, fixes go in new commits (not amendments).

## Core Process

### 1. Understand the change

Read the spec/plan for context. Then read the diff:
```bash
git diff main...HEAD
```
Understand WHAT changed and WHY before evaluating HOW.

### 2. Five-axis review

**Correctness:**
- Does the code do what the spec requires?
- Are edge cases handled?
- Could it fail silently? (error swallowed, default value hiding a problem)
- Are race conditions possible?

**Design:**
- Does it fit the existing architecture? (check CODEBASE.md)
- Are boundaries clean? (clear inputs/outputs, minimal coupling)
- Is the abstraction level appropriate? (not too abstract, not too concrete)
- Chesterton's Fence: if you don't understand why something exists, don't remove it

**Complexity:**
- Could someone understand this without the author explaining it?
- Is there unnecessary indirection?
- Are there simpler alternatives that achieve the same result?

**Naming:**
- Do names communicate intent?
- Consistent with codebase conventions?
- No abbreviations that require explanation?

**Test coverage:**
- Are changed code paths tested?
- Do tests verify behavior (not implementation)?
- Are edge cases in the tests?
- See `references/testing-patterns.md`

### 3. Classify findings

| Severity | Meaning | Example |
|----------|---------|---------|
| **BLOCKING** | Must fix before merge | Missing auth check, data loss risk |
| **SHOULD FIX** | Fix before ship | Poor error message, missing edge case test |
| **NIT** | Improve if convenient | Naming suggestion, style preference |

### 4. High-risk check

If the change touches any of these, trigger Codex adversarial review:
- Authentication or authorization
- Payment processing
- Concurrency or shared state
- Caching (invalidation is hard)
- Public API contracts
- Data migrations
- Cryptographic operations

### 5. Summarize

```
## Review Summary
- Overall: APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
- Blocking issues: [count]
- Should-fix issues: [count]
- Nits: [count]
- Codex review: [requested / not needed]
```

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "The tests pass, so it's correct" | Tests verify tested paths. They don't prove the absence of bugs. |
| "I wrote it, I know it's fine" | Self-review catches ~30% of issues. Fresh eyes catch more. |
| "This is simple, it doesn't need review" | Simple code with subtle bugs is the hardest to debug later. |
| "Codex review is overkill" | For auth and payments, overkill is the right amount. |

## Red Flags

- Reviewing only the happy path
- No test coverage check on changed lines
- Skipping Codex adversarial review on high-risk code
- Approving without understanding the full change
- All feedback is nits, no structural review

## Verification

- [ ] Full diff reviewed (not just new files)
- [ ] All five axes checked (correctness, design, complexity, naming, tests)
- [ ] Findings classified by severity
- [ ] High-risk code triggers Codex adversarial review
- [ ] Review summary provided with clear recommendation
