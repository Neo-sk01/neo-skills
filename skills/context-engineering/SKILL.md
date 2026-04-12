---
name: context-engineering
description: Manages what information is loaded into context and when, preventing both starvation and flooding. Use when starting a task, switching contexts, or when output quality degrades.
---

# Context Engineering

## Overview

Context is the single biggest lever for agent output quality. Too little and the agent hallucinates. Too much and it loses focus. This skill manages what information enters context, when, and how much.

## When to Use

- Starting any implementation session
- Output quality is degrading (hallucinated APIs, wrong patterns)
- Switching between tasks or areas of the codebase
- Setting up a new project or onboarding to an existing one

## When NOT to Use

- Already mid-task with good output quality — don't interrupt flow

## Integration Points

**Context7:** Primary tool for documentation verification. Use `resolve-library-id` + `query-docs` to load current, accurate library documentation instead of relying on training data.

**CODEBASE.md:** The `context-handoff` skill's CODEBASE-TEMPLATE.md defines the project map. This is the first file to load when starting a session.

## Core Process

### 1. Load the right context

Priority order for session start:
1. **Rules files** — CLAUDE.md (project conventions, always loaded)
2. **CODEBASE.md** — architecture, dependency map, conventions
3. **PLAN.md** — current task and its context
4. **HANDOFF.md** — previous session state (if resuming)
5. **Task-relevant source files** — only what this task touches

Target: <2000 lines of loaded context per task.

### 2. Brain dump pattern

When starting a new session or task, consolidate what the agent needs into one structured block:

```
CONTEXT FOR THIS TASK:
- Tech stack: [languages, frameworks, versions]
- What I'm building: [task description from plan]
- Key files: [paths]
- Patterns to follow: [from CODEBASE.md]
- Gotchas: [from FAILURES.md or known issues]
```

### 3. Refresh on task switch

When moving to a new task:
1. Mentally unload previous task's files
2. Load new task's relevant files
3. Re-read PLAN.md for the new task's context
4. Context7 check if the new task involves different libraries

### 4. Monitor for degradation

Watch for signs of context problems:

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Hallucinated APIs or methods | Context starvation | Context7 verify the API |
| Wrong patterns or conventions | Missing CODEBASE.md | Reload conventions |
| Repeating mistakes | FAILURES.md not loaded | Load failed approaches |
| Drifting from task | Context flooding | Reduce loaded files |
| Inconsistent naming | Stale context | Refresh from source files |

### 5. Context7 verification protocol

Before using any library API:
1. `resolve-library-id(libraryName)` — cache per session
2. `query-docs(libraryId, "specific method with args and version")` — be specific
3. Score confidence: HIGH (exact match) / MEDIUM (partial) / LOW (extrapolating) / NONE (no results)
4. If LOW/NONE: don't use it without manual verification

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I know this API well" | Training data may not reflect the installed version. Context7 check takes 10 seconds. |
| "More context is better" | More context means more noise. Load only what the task needs. |
| "I'll check the docs if something breaks" | Check BEFORE it breaks. Prevention is cheaper than debugging. |
| "This is boilerplate, I don't need context" | Boilerplate with wrong patterns causes the most insidious bugs. |

## Red Flags

- Starting implementation without loading CODEBASE.md
- Using library APIs without Context7 verification
- Loading >3000 lines of source files for a single task
- Not refreshing context when switching tasks
- Ignoring LOW/NONE confidence scores from Context7

## Verification

- [ ] CODEBASE.md loaded at session start
- [ ] Context7 used for every library API before implementation
- [ ] Task context under 2000 lines of source
- [ ] Context refreshed between task switches
- [ ] No hallucinated APIs in the implementation
