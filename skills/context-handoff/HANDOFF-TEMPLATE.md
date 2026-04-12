# Handoff Template

Generate this document at every session end. Fill in all sections — skip none.

---

```markdown
# Handoff: {repo_name}
Generated: {ISO timestamp}
Session: {sequential number if known}
Type: clean | blocked | partial
Epic: {name from EPIC.md}
Plan progress: {completed task #} of {total tasks}
Plan mutations this session: {count, or "none"}

## Pipeline Position
- Superpowers stage: {brainstorming | planning | executing | reviewing | finishing}
- Active skill: {e.g., test-driven-development}
- TDD cycle: {RED | GREEN | REFACTOR | N/A}
- Current plan task: {task # and one-line description}
- Code review: {not-started | passed | feedback-pending | feedback-applied}
- Codex review: {not-requested | pending (job ID) | passed | issues-found}
- Last integration checkpoint: {passed | failed (detail) | not-yet-run}
- Worktree: {branch name} at {path}

## Context Budget
- Estimated tokens consumed this session: {n}
- Tasks completed: {n} in ~{n} tokens each
- Average tokens per task: {n}

## Session Cost
- Tokens consumed: ~{n}
- Entire checkpoints: {count}
- Codex jobs: {count}
- Tasks completed: {count}
- Cost per task: ~{n} tokens
- Efficiency note: {observation}

## Entire Checkpoints (this session)
| Checkpoint ID | Commit | What Was Done |
|---------------|--------|---------------|
| {12-char hex} | {short SHA} {message} | {1-line summary} |

Latest checkpoint: `{ID}`

## Codex Jobs (this session)
| Job ID | Type | Status | Key Findings |
|--------|------|--------|-------------|
| {id} | {type} | {status} | {summary} |

(or "None this session")

## Last Commit
`{short SHA}` — {commit message}
Entire-Checkpoint: {checkpoint ID}
Entire-Attribution: {agent %}

## Dirty Files
- {path} — {what's in progress}
(or "None — working tree clean")

## Completed This Session
- [x] Plan task #{n}: {description}
- [x] Plan task #{n}: {description}

## Failed Approaches This Session
| Task | Approach | Why It Failed | Time Spent | Rollback Checkpoint |
|------|----------|---------------|-----------|-------------------|
| {task} | {approach} | {reason} | {est.} | {checkpoint ID} |

(or "None")

## Plan Mutations This Session
| Original Task | Change | Reason |
|--------------|--------|--------|
| Task #{n} | {change} | {reason} |

(or "None — plan unchanged")

## Human Tasks Queue
| Task | Why Agent Can't Do It | Blocks | Priority |
|------|----------------------|--------|----------|
| {task} | {reason} | Plan task #{n} | Before next session |

(or "None — no human tasks pending")

## Current Task
{If clean: "None — ready for plan task #{next}."}
{If blocked: state the blocker clearly.}
{If partial: exact stopping point + TDD cycle position.
 "Mid-TDD RED: test at {path} expects {behavior}, implementation not started."
 "Mid-TDD GREEN: {path} passes, refactor pending."}

## Next Actions
1. {If human tasks pending: "Resolve: {task} (blocks plan task #{n})"}
2. {If Codex job pending: "Check /codex:status for job {id}"}
3. {If LOW doc-check: "Re-verify {library} {method}"}
4. {Plan task # — concrete step}

## Decisions Made
| Decision | Rationale | Reversible? |
|---|---|---|
| {choice} | {why} | yes/no |

## Bugs Investigated
| Symptom | Root Cause | Fix | Codex Parallel? | Codebase Learning |
|---------|-----------|-----|-----------------|-------------------|
| {bug} | {cause} | {fix} | {yes/no} | {pattern} |

(or "None")

## Doc Verifications (Context7)
| Library | Context7 ID | Query | Finding | Installed Ver | Confidence |
|---------|------------|-------|---------|---------------|------------|
| {lib} | {id} | {query} | {finding} | {ver} | HIGH/MED/LOW/NONE |

## Integration Checkpoint Results
- Last run: {timestamp}
- Full test suite: {pass} passing, {fail} failing
- Build: passing | failing
- Cross-system deps checked: {list from CODEBASE.md map}
- Issues found: {list, or "none"}

## Test Manifest
- Command: `{command}`
- Passing: {count}, Failing: {count}
- Passing test hash: {SHA-256}
- Regressed since last session: {list or "none"}
- New tests added: {list or "none"}

## Codebase Updates
{Discoveries for CODEBASE.md, including dependency map changes. "None" if nothing.}

## Env Snapshot
- Runtime: {language + version}
- Packages changed: {added/removed/updated}
- Lockfile hash: {SHA-256}
- Dependency changes on resume: {list or "none"}
- Env vars: {list}
- Build: passing | failing ({reason})

## Context for Next Session
{2-3 sentences. Written AFTER compression pass.
What would you tell a teammate? Note pending Codex jobs,
human tasks, LOW doc-checks, hunches.
For full reasoning, point to Entire checkpoint.}
```

---

## Save Protocol

**Archive first, then overwrite.**

```bash
REPO_ROOT=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_ROOT")
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
OBS_PROJECT="$OBSIDIAN_VAULT/Projects/$REPO_NAME"

# 1. Git repo
cp HANDOFF.md "$REPO_ROOT/HANDOFF.md"

# 2. Obsidian archive FIRST
mkdir -p "$OBS_PROJECT/handoff-archive"
cp HANDOFF.md "$OBS_PROJECT/handoff-archive/$TIMESTAMP.md"

# 3. Obsidian current (overwrite)
cp HANDOFF.md "$OBS_PROJECT/HANDOFF.md"

# 4. Update CODEBASE.md if "Codebase Updates" non-empty
# 5. Update FAILURES.md if "Failed Approaches" non-empty
# 6. Append to decisions-log.md (dedup)
# 7. Update PLAN.md progress markers + mutations
```
