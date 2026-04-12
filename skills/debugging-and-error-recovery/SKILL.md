---
name: debugging-and-error-recovery
description: Systematic five-step triage for bugs and failures with rollback protocol. Use when something is broken or behaving unexpectedly.
---

# Debugging and Error Recovery

## Overview

Reproduce, localize, hypothesize, fix, guard. Don't guess — trace. Don't shotgun — isolate. Every fix gets a regression test. If 3 fix attempts fail, roll back to the last known-good state.

## When to Use

- Tests are failing
- Runtime errors or unexpected behavior
- Build failures
- Something that used to work doesn't anymore

## When NOT to Use

- New feature development — go to `incremental-implementation`
- Code quality issues without bugs — go to `code-simplification`

## Integration Points

**Codex:** For complex bugs, run parallel investigation:
```
/codex:rescue --background investigate why [specific test/behavior] fails
```
Two models approaching the same bug from different angles.

**Entire:** Checkpoints are your rollback points. Every commit before the bug is a known-good state you can return to.

**Context7:** If the hypothesis involves library behavior, verify with Context7 before attempting a fix. Don't fix code that's actually correct — the library might have changed.

## Core Process

### 1. REPRODUCE

Before anything else, reproduce the bug reliably:
- What is the exact error message or unexpected behavior?
- What is the exact command or action that triggers it?
- Can you reproduce it consistently?

If you can't reproduce it, you can't fix it. Investigate environmental differences.

### 2. LOCALIZE

Narrow the search space:
- When did it last work? (`git log`, `git bisect`)
- What changed between working and broken? (`git diff`)
- Where does the error originate? (read the stack trace — the FULL trace, not just the first line)
- Add logging or breakpoints to trace the execution path

### 3. HYPOTHESIZE

Form a specific, testable hypothesis:
```
HYPOTHESIS: [specific claim about what's wrong]
TEST: [how to confirm or refute]
EXPECTED if correct: [what you'll see]
EXPECTED if wrong: [what you'll see instead]
```

If the hypothesis involves a library: Context7 check before testing.

### 4. FIX

Apply the minimum change that addresses the root cause:
- Don't fix symptoms — fix causes
- Don't refactor while fixing — scope discipline
- Don't change unrelated code

### 5. GUARD

Write a regression test that:
- Reproduces the original bug (would have failed before the fix)
- Passes with the fix in place
- Prevents the same bug from recurring

Run the full test suite. No new failures.

### Rollback Protocol

**Trigger: 3 consecutive fix attempts on the same failure.**

1. STOP — no more fix attempts
2. Find the last green checkpoint: `git log` or `entire explain`
3. Revert: `git reset --hard <last-green-SHA>`
4. Log in FAILURES.md:
   - What was tried
   - Why it failed
   - Time spent
   - Checkpoint ID
5. Re-approach from clean state with a different strategy
6. If same task fails 2x with different approaches: escalate to user or `/codex:rescue`

### Failure Escalation

```
1 failure  -> normal debugging
2 failures -> re-read hypothesis, Context7 verify
3 failures -> ROLLBACK + different approach
2 rollbacks on same task -> BLOCKED -> escalate
```

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I know what's wrong, let me just fix it" | Hypothesis without reproduction is guessing. Reproduce first. |
| "Let me try a quick fix" | Quick fixes without diagnosis create new bugs. Follow the process. |
| "The error message is clear enough" | Read the FULL stack trace. The real cause is often 5 frames deep. |
| "I'll skip the regression test, the fix is obvious" | Obvious fixes without tests regress. Every fix gets a test. |
| "One more attempt will work" | That's attempt #4. Rollback protocol exists for a reason. |

## Red Flags

- Fixing without reproducing first
- Reading only the first line of a stack trace
- Changing multiple things at once (can't tell which fixed it)
- No regression test for the fix
- More than 3 fix attempts without rollback
- Fixing symptoms instead of root cause

## Verification

- [ ] Bug was reproduced reliably before attempting a fix
- [ ] Root cause was localized (not just symptoms)
- [ ] Hypothesis was explicitly stated before fix attempt
- [ ] Fix addresses root cause, not symptoms
- [ ] Regression test written and passing
- [ ] Full test suite passes (no new failures)
- [ ] Rollback protocol followed if 3 attempts failed
