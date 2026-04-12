---
name: incremental-implementation
description: Builds features through thin vertical slices, testing each before expanding. Use when implementing tasks from an approved plan.
---

# Incremental Implementation

## Overview

Build in thin vertical slices — each slice delivers a working, tested increment from input to output. Never build the full horizontal layer (all models, then all routes, then all UI). Instead, build one complete path through the stack, prove it works, then expand.

## When to Use

- You have an approved plan from `planning-and-task-breakdown`
- Building new features or significant changes
- Any implementation that touches multiple layers

## When NOT to Use

- Single-layer changes (just a CSS fix, just a config change)
- Bug fixes — go to `debugging-and-error-recovery`

## Integration Points

**Entire:** Commit after every slice. Each commit creates a checkpoint. If you need to roll back, you have a clean point.

**Context7:** Before implementing any library call, verify the API: `query-docs(libraryId, "specific method with args")`. Don't guess at signatures.

**Superpowers:** This runs inside the executing-plans or subagent-driven-development phase. Follow plan mutation protocol if the plan needs to change.

## Core Process

### 1. Load context

Read the plan task you're implementing. Load relevant files. Check CODEBASE.md for conventions and patterns.

Budget context: aim for <2000 lines of loaded context per task. More files does not mean better output.

### 2. Build the thinnest slice

Pick the simplest possible path through the feature:
- Hardcode what you can
- Skip error handling initially
- Use the simplest data (one item, not a list)
- Get it working end-to-end

### 3. Test the slice

Write a test that proves the slice works. Follow TDD if using `test-driven-development` skill (write test first, see it fail, implement, see it pass).

Run the test. It must pass.

### 4. Commit

```bash
git add [specific files]
git commit -m "feat: [what the slice does]"
```

### 5. Expand

Pick the next most valuable expansion:
- Replace hardcoded values with real logic
- Add error handling for the most likely failure
- Handle the next data case

Each expansion follows the same cycle: implement -> test -> commit.

### 6. Track budget

After each task, estimate context consumed:
- >5k tokens per remaining task: COMFORTABLE — continue
- 2k-5k: CAUTION — finish current task, evaluate handoff
- <2k: HANDOFF NOW — finish atomic unit, generate handoff

### 7. Plan mutation

If you discover the plan is wrong (task is harder than expected, dependency was missed, API doesn't work as planned):

1. Stop implementation
2. Document what changed and why
3. Update the plan with `[MUTATED]` markers
4. If >3 tasks affected, pause and present to user
5. Log in decisions-log.md

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "Let me build the full data layer first" | Horizontal layers are untestable in isolation. Build vertically. |
| "I'll add tests after" | Tests after are tests never. Test each slice. |
| "This slice is too small to commit" | Small commits are rollback points. Commit after every working increment. |
| "I'll handle errors at the end" | Error handling at the end means you don't know if it works. Add it incrementally. |

## Red Flags

- Building full horizontal layers before any vertical slice
- More than 30 minutes since last commit
- Multiple files changed without a test
- Context budget not tracked
- Plan changed without mutation protocol

## Verification

- [ ] Each slice produces a working increment
- [ ] Tests pass after every slice
- [ ] Commits happen after every working increment
- [ ] Context budget tracked throughout the session
- [ ] Plan mutations follow the formal protocol
