---
name: planning-and-task-breakdown
description: Decomposes specs into small, verifiable tasks with acceptance criteria and handoff-safe boundaries. Use when you have an approved spec and need an implementation plan.
---

# Planning and Task Breakdown

## Overview

Break a spec into bite-sized tasks that can each be implemented, tested, and committed independently. Each task produces a working, verifiable increment. The plan marks handoff-safe boundaries so sessions can be interrupted cleanly.

## When to Use

- You have an approved spec from `spec-driven-development`
- The work requires more than one commit
- You need to coordinate multi-step implementation

## When NOT to Use

- No spec exists yet — go to `spec-driven-development`
- Single-commit change — just do it
- Bug fix — go to `debugging-and-error-recovery`

## Integration Points

**Entire:** Every commit creates an Entire checkpoint. The plan marks `--- HANDOFF SAFE ---` boundaries at points where the codebase is in a clean, deployable state.

**Superpowers:** This maps to the writing-plans phase. The plan feeds into `incremental-implementation`.

**Context7:** Verify every library method referenced in the plan. If a method doesn't exist or works differently than assumed, fix the plan now — not during implementation.

## Core Process

### 1. Read the spec

Load the full spec. Identify:
- All acceptance criteria (these become verification targets)
- Technical approach (this drives task decomposition)
- Risks (these may need spike tasks)

### 2. Decompose into tasks

Each task must:
- Be completable in one session (~30 min to 2 hours)
- Produce a working increment (tests pass before and after)
- Have clear acceptance criteria derived from the spec
- Be independently understandable (no "similar to task N")

Task format:
```markdown
### Task N: [Component/Feature]
**Files:** [exact paths — create/modify/test]
**Acceptance criteria:** [specific, testable]
**Depends on:** [task numbers, or "none"]
**Handoff safe after:** [yes/no]
```

### 3. Mark boundaries

Add `--- HANDOFF SAFE ---` after tasks where:
- All tests pass
- The codebase is in a deployable state
- A new session could resume without context loss

Add `--- INTEGRATION CHECKPOINT ---` after:
- Every 3-5 tasks
- Any task modifying shared interfaces (APIs, DB schema, auth, routing)

### 4. Verify the plan

- Does every acceptance criterion from the spec map to at least one task?
- Are dependencies between tasks explicit?
- Is any task too large (>2 hours)? Split it.
- Context7 check on every library method referenced.

### 5. Present for approval

Show the full plan. Get user approval before starting implementation.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I can figure out the tasks as I go" | That's how you end up with half-done features and broken tests. |
| "The plan will change anyway" | Plans change through mutation protocol, not by being absent. |
| "This is too small for a plan" | If it's more than one commit, it needs a plan. |
| "Breaking it down takes too long" | A 15-minute plan prevents hours of rework. |

## Red Flags

- Tasks without acceptance criteria
- Tasks with vague scope ("set up the backend")
- No handoff-safe boundaries marked
- Dependencies not explicit
- No integration checkpoints in a plan with >5 tasks
- Starting implementation before the plan is approved

## Verification

- [ ] Every spec acceptance criterion maps to at least one task
- [ ] Every task has specific acceptance criteria
- [ ] Dependencies between tasks are explicit
- [ ] Handoff-safe boundaries are marked
- [ ] Integration checkpoints are placed every 3-5 tasks
- [ ] Context7 verified library methods referenced in the plan
- [ ] No task exceeds ~2 hours estimated effort
- [ ] User has approved the plan
