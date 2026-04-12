---
name: test-driven-development
description: Enforces RED-GREEN-REFACTOR cycle — failing test first, then minimal implementation, then cleanup. Use when writing or running tests.
---

# Test-Driven Development

## Overview

Write the test before the code. Watch it fail (RED). Write the minimum code to make it pass (GREEN). Clean up (REFACTOR). This is not optional when this skill applies — the cycle enforces correctness.

## When to Use

- Implementing any new functionality
- Adding behavior to existing code
- Fixing a bug (write a test that reproduces it first)
- Used alongside `incremental-implementation`

## When NOT to Use

- Exploratory prototyping (throwaway code you'll delete)
- Pure configuration changes with no logic
- Documentation-only changes

## Integration Points

**Context7:** At the RED phase, before writing the test, verify the expected API behavior: `query-docs(libraryId, "specific method behavior")`. Don't write a test against an API you haven't verified.

**Codex:** If 3 consecutive attempts to make a test pass fail, trigger Codex rescue:
```
/codex:rescue --background [test path] fails with [error] — 3 attempts exhausted
```
Two models approaching the same problem from different angles.

**Entire:** Every GREEN -> commit creates an Entire checkpoint. This is your rollback safety net.

## Core Process

### 1. RED — write the failing test

Before writing any implementation:

1. Context7 check: verify the API/behavior you're testing against
2. Write one test for one specific behavior
3. Name it descriptively: `test_[unit]_[scenario]_[expected]`
4. Use Arrange-Act-Assert structure
5. Run the test
6. **Confirm it fails for the right reason** — "function not defined" or "expected X got Y", NOT a syntax error or import error

If the test fails for the wrong reason, fix the test before proceeding.

### 2. GREEN — minimal implementation

Write the absolute minimum code to make the test pass:
- Hardcode return values if that's all the test requires
- Don't add logic the test doesn't demand
- Don't handle errors the test doesn't check
- Don't optimize

Run the test. It must pass. All existing tests must also pass.

### 3. REFACTOR — clean up

Now that you have a green test suite:
- Remove duplication
- Improve names
- Extract if complexity warrants it
- DO NOT change behavior (tests should still pass without modification)

Run all tests after refactoring. Everything must still pass.

### 4. COMMIT

```bash
git add [test file] [implementation file]
git commit -m "feat: [what behavior was added]"
```

### 5. Repeat

Next behavior -> next test -> RED -> GREEN -> REFACTOR -> COMMIT.

### Failure Protocol

If a test won't pass after 3 attempts:

1. STOP — no more fix attempts
2. Check: is the test correct? (Context7 re-verify)
3. Check: is the approach correct? (re-read the plan)
4. If both correct, trigger Codex rescue
5. If the test is wrong, fix the test and reset the count

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I'll write tests after the implementation" | Tests written after are weaker — they test what you built, not what you intended. |
| "This is too simple to need TDD" | Simple code with no tests has no safety net for future changes. |
| "TDD is slower" | TDD is slower per line. It's faster per working feature. |
| "I know it works, I just ran it" | Manual testing is not repeatable. Write the test. |
| "The test is obvious, let me skip to GREEN" | The RED step catches wrong assumptions. Never skip it. |

## Red Flags

- Writing implementation before writing the test
- Test passes on first run (never saw RED)
- Test fails for the wrong reason and you proceed anyway
- Multiple behaviors tested in one test
- Refactoring changes behavior (tests modified during REFACTOR)
- More than 3 attempts to pass a test without escalation

## Verification

- [ ] Every new behavior has a test written BEFORE the implementation
- [ ] Every test was seen to fail (RED) before passing (GREEN)
- [ ] Tests fail for the RIGHT reason (not syntax/import errors)
- [ ] Refactoring doesn't change behavior (no test modifications in REFACTOR)
- [ ] All existing tests still pass after each cycle
- [ ] Commits happen after every GREEN -> REFACTOR cycle
- [ ] Failure protocol triggered after 3 consecutive failures
