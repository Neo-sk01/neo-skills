# Reference: Detailed Protocols

Referenced by `SKILL.md`. Contains the full specification for each protocol.

---

## Context Budget Tracking

```
At session start:
  - Estimate total context window (e.g., 200k tokens)
  - Note tokens consumed by loaded docs
  - Remaining = total - loaded

During work:
  - After each task, estimate tokens consumed
  - Calculate: remaining budget vs remaining plan tasks

Thresholds:
  - >5k tokens per remaining task: COMFORTABLE — continue
  - 2k-5k: CAUTION — finish current task, evaluate
  - <2k: HANDOFF NOW — finish atomic unit, generate handoff
  - If only 1 task remains and budget tight: push to finish
```

Record in handoff:
```
## Context Budget
- Estimated tokens consumed: {n}
- Tasks completed: {n} in ~{n} tokens each
- Average tokens per task: {n}
```

---

## Rollback Protocol

### When to trigger
```
ROLLBACK if ANY:
1. Tests fail after 3 consecutive fix attempts on same failure
2. A fix introduces more failing tests than it resolves
3. Approach fundamentally contradicts plan or CODEBASE.md conventions
4. Context7 doc-check reveals API doesn't support required behavior
```

### How to execute
```
1. STOP — no more fix attempts
2. Find last green checkpoint (Entire checkpoints table or git log)
3. Revert: git reset --hard <last-green-SHA> or Entire rewind
4. Log in FAILURES.md:
   - What was tried, why it failed, time spent, checkpoint ID
5. Re-approach from clean state with different strategy
6. If same task fails 2x with different approaches:
   → Mark BLOCKED → escalate to user or /codex:rescue
```

---

## Plan Mutation Protocol

### When to trigger
```
MUTATE PLAN if ANY:
1. Task significantly harder than estimated (>3x effort)
2. Dependency between tasks was missed
3. New requirement emerged
4. Context7 reveals API doesn't work as planned
5. Rollback forced different approach
6. Code review identified structural issue
```

### How to execute
```
1. Document: what changed, why, impact on boundaries
2. Update PLAN.md:
   - Mark original as MUTATED with reason
   - Add new tasks: "[ADDED session {n}]"
   - Re-verify "--- HANDOFF SAFE ---" boundaries
3. Re-run Context7 doc-check on new API references
4. If large (>3 tasks affected): PAUSE, present to user
5. Log in decisions-log.md
```

---

## Integration Checkpoints

### When to run
```
After:
1. Each task batch (3-5 tasks completed)
2. Any task modifying shared interfaces (APIs, DB schema, auth, routing)
3. Before any handoff
4. Before finishing-a-development-branch
```

### What to validate
```
1. Full test suite (all existing tests, not just new)
2. Build/compile succeeds
3. Cross-system dependency check (from CODEBASE.md dependency map):
   - API contracts match between services?
   - DB migrations consistent with code?
   - Third-party integration endpoints work?
   - Environment variables present and correct?
4. Lint/format against codebase conventions
5. Integration or e2e tests if project has them
```

---

## Human Tasks Queue

### What goes in
```
Tasks requiring:
- Manual testing (visual/UX, device, accessibility)
- Production credentials or infrastructure access
- Business decisions (pricing, feature flags, A/B params)
- Third-party coordination (API keys, vendor approvals, legal)
- Security-sensitive operations (key rotation, certificates)
- Deployment decisions (rollout, canary, feature gates)
- Design judgment (UI/UX choices)
```

### How it works
```
1. During execution, when agent identifies human-required task:
   - Mark plan task as HUMAN-REQUIRED with reason
   - Continue with non-dependent tasks
   - Note dependent tasks as BLOCKED-BY-HUMAN-TASK
2. Record in HANDOFF.md (see template)
3. On resume: check which resolved → unblock dependent work
```

---

## Auto-Documentation

### What to generate at epic completion
```
1. README updates (if features added)
2. API documentation (new endpoints: request/response contracts)
3. Architecture notes (if structure changed → update CODEBASE.md)
4. Migration guide (if breaking changes)
5. Inline doc comments matching codebase style (JSDoc, docstrings, etc.)
```

### How to align with conventions
```
1. Check CODEBASE.md for existing doc patterns
2. Generate docs matching those patterns exactly
3. Context7 doc-check on library-specific doc syntax
4. Include in final commit before finishing branch
```

---

## Failed Approaches Log (FAILURES.md)

File: `$OBS_PROJECT/FAILURES.md`

```markdown
# Failed Approaches: {repo_name}

## {date} — Session {n} — Epic: {name}
### {task or problem description}

**Approach tried:** {what was attempted}
**Why it failed:** {concrete reason — test output, error, API limitation}
**Time spent:** {estimate}
**What worked instead:** {successful approach, or "still unresolved"}
**Entire checkpoint:** {ID of commit before rollback}
**Codex investigated?:** {yes/no — same finding?}
```

Load on every resume alongside CODEBASE.md.

---

## Decisions Log

File: `$OBS_PROJECT/decisions-log.md`

```markdown
# Decisions Log: {repo_name}

## {date} — Session {n} — Epic: {name}
Entire checkpoint: {ID}

| Decision | Rationale | Reversible? |
|---|---|---|

### Bugs & Codebase Learnings
| Bug | Root Cause | Pattern for CODEBASE.md |
|-----|-----------|------------------------|

### Codex Review Patterns
| Finding | Sessions Seen | Systemic? | Action Taken |
|---------|--------------|-----------|-------------|
```

Dedup before appending. Track Codex finding patterns — same issue 2+ sessions = systemic.

---

## Session Cost Tracking

Record in handoff:
```
## Session Cost
- Tokens consumed: ~{n}
- Entire checkpoints: {count}
- Codex jobs: {count}
- Tasks completed: {count}
- Cost per task: ~{n} tokens
- Efficiency note: {observation for future sessions}
```

---

## Pre-Handoff Context Compression

Before generating HANDOFF.md:
```
1. Re-read PLAN.md — confirm which tasks complete vs remaining
2. Re-read CODEBASE.md — confirm any updates needed
3. Review session's decisions, bugs, failures, doc-checks
4. For each HANDOFF.md section, ask:
   - Most information-dense expression?
   - Duplicating what Entire captured?
   - Would next session understand without me?
5. Write "Context for Next Session" LAST — after compression
```
