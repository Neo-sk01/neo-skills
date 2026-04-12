# Reference: Detailed Protocols

Referenced by `SKILL.md`. Contains the full specification for each protocol.

---

## Context Budget Tracking

```
At session start:
  - Estimate total context window (e.g., 200k tokens)
  - Note tokens consumed by loaded docs
  - Remaining = total - loaded
  - Reserve 10-20% of the window for debugging/review surprises
  - Set a target reload budget for the next session

During work:
  - After each task, estimate tokens consumed
  - Calculate: remaining budget vs remaining plan tasks
  - Re-estimate whether the next session's reload packet still fits comfortably
  - If handoff/supporting docs are getting bloated, compress or move detail into referenced files

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
- Estimated tokens loaded at resume start: {n}
- Target reload budget for next session: {n}
- Reserved buffer for unexpected work: {n}
- Entire input tokens: {n}
- Entire output tokens: {n}
- Entire cache creation tokens: {n}
- Entire cache read tokens: {n}
- Entire API calls: {n}
- Tasks completed: {n} in ~{n} tokens each
- Average tokens per task: {n}
```

---

## Handoff Quality Gate

A handoff is only complete if a fresh agent can answer all five questions quickly and knows exactly where to dig deeper in Entire:

1. What exact task or blocker am I resuming?
2. What should I do first when this session starts?
3. Which files and which Entire checkpoints/transcript segments must I load, in what order, to regain enough context?
4. What was verified already, and what must be re-verified?
5. How much context budget should I expect to spend reloading this work?

If any answer is missing, the handoff is incomplete.

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
3. Capture current state before changing anything:
   - save a patch, stash, or branch with the current diff
   - note dirty files and ownership
4. Restore safely:
   - use `entire rewind` only if you intentionally want to move backward within the active session and understand its discard semantics
   - prefer file-scoped restore of task-owned files
   - if ownership is unclear or unrelated edits are present, create a fresh worktree from the last green checkpoint instead of reverting the current tree
   - NEVER use git reset --hard unless the human explicitly asked for it
5. Log in FAILURES.md:
   - What was tried, why it failed, time spent, checkpoint ID
6. Re-approach from clean state with different strategy
7. If same task fails 2x with different approaches:
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

## Memory Root

Use a repo-local memory directory even when Obsidian is unavailable:

```bash
REPO_ROOT=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_ROOT")
MEMORY_ROOT="$REPO_ROOT/.agent-memory"
```

If `OBSIDIAN_VAULT` is set, mirror repo-local memory into:

```bash
OBSIDIAN_PROJECT="$OBSIDIAN_VAULT/Projects/$REPO_NAME"
```

Git + repo-local memory are the required persistence layer. Obsidian is an optional mirror.

---

## Resume Order

Resume in layers:

1. Read `HANDOFF.md`
2. Run `entire status`
3. Load the files listed in the Compact Reload Packet
4. Inspect the referenced Entire checkpoints / `entire explain` outputs:
   - start with `entire explain --short`
   - escalate to `--full` or `--raw-transcript` only when the handoff says deeper reasoning is needed
5. Only then widen the archaeology scope with `--session` or `--search-all` if something is still ambiguous
6. If state looks stale or broken, run `entire doctor`

The handoff is the index. Entire is the archive. Use both.

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

Primary file: `$MEMORY_ROOT/FAILURES.md`
Optional mirror: `$OBSIDIAN_PROJECT/FAILURES.md`

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

Primary file: `$MEMORY_ROOT/decisions-log.md`
Optional mirror: `$OBSIDIAN_PROJECT/decisions-log.md`

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
- Input tokens: {n}
- Output tokens: {n}
- Cache creation tokens: {n}
- Cache read tokens: {n}
- API calls: {n}
- Entire checkpoints: {count}
- Codex jobs: {count}
- Tasks completed: {count}
- Cost per task: ~{n} tokens
- Efficiency note: {observation for future sessions}
```

---

## Compact Reload Packet

This is the handoff section that keeps the next session's context window clean.

Rules:
- It must be readable in under 60 seconds.
- It must list required files in load order.
- It must list the Entire checkpoints, commands, or transcript targets needed for deeper reasoning.
- It must prefer `entire explain --short` for first-pass archaeology, then explicitly say when to escalate to `--full` or `--raw-transcript`.
- It must state the exact next action, not just the current topic.
- It must call out blockers, pending jobs, LOW/NONE doc-checks, and re-verification needs.
- It must be compact: summarize, then point to files/checkpoints for detail.

If the host supports built-in conversation compaction/summarization, run it only after the handoff has been generated and verified.

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
5. Write the compact reload packet before any optional narrative
6. Point the next session to the exact Entire archaeology targets it should inspect first
7. Write "Context for Next Session" LAST — after compression, max 120 words
```
