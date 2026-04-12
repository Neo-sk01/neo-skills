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

## Compact Reload Packet
- Exact next action: {single concrete action the next session should do first}
- Resume commands:
  - `entire status`
  - `entire explain --commit {sha} --short`
- Required reload order:
  1. `HANDOFF.md` — current state and verification evidence
  2. `{path}` — {why this file is required next}
  3. `{path}` — {why this file is required next}
- Entire archaeology targets:
  1. `entire explain --commit {sha} --short` — {what compact reasoning this recovers}
  2. `entire explain --checkpoint {id} --full` — {what deeper reasoning this recovers}
  3. `entire explain --checkpoint {id} --raw-transcript` — {use only if exact raw transcript details are needed}
- Archaeology escalation rule: {when the next session should inspect Entire before acting, and when it can proceed directly from the handoff}
- Optional reloads:
  - `{path}` — {only if investigating X}
  - `{path}` — {only if re-checking Y}
- Avoid reloading: {transcript/history or low-value files that are no longer needed}
- Resume budget target: ~{n} tokens for handoff/docs + ~{n} tokens for targeted Entire archaeology if needed

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
- Efficiency note: {observation}

## Entire Checkpoints (this session)
| Checkpoint ID | Commit | What Was Done |
|---------------|--------|---------------|
| {12-char hex} | {short SHA} {message} | {1-line summary} |

Latest checkpoint: `{ID}`

## Entire Status
- Last `entire status` result: {healthy | warning | blocked}
- `entire status --detailed` reviewed this session: yes/no
- `entire doctor` needed: yes/no
- Shadow branch note: {current shadow branch state if relevant, or "none noted"}

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

## Compression Pass
- Compact reload packet verified: yes/no
- Entire archaeology targets verified: yes/no
- Host compact/summarize feature run after handoff write: yes/no/n-a
- Detailed reasoning intentionally left in: {Entire checkpoint IDs / referenced docs}

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
{2-3 sentences, max 120 words. Written AFTER compression pass.
What would you tell a teammate? Note pending Codex jobs,
human tasks, LOW doc-checks, hunches.
For full reasoning, point to specific Entire checkpoints or `entire explain` targets.}
```

---

## Save Protocol

**Archive first, then overwrite.**

```bash
REPO_ROOT=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_ROOT")
TIMESTAMP=$(date +%Y%m%d-%H%M%S)
MEMORY_ROOT="$REPO_ROOT/.agent-memory"

# 1. Archive previous repo handoff FIRST
mkdir -p "$MEMORY_ROOT/handoff-archive"
if [ -f "$REPO_ROOT/HANDOFF.md" ]; then
  cp "$REPO_ROOT/HANDOFF.md" "$MEMORY_ROOT/handoff-archive/$TIMESTAMP.md"
fi

# 2. Write current handoff to repo root
cp HANDOFF.md "$REPO_ROOT/HANDOFF.md"

# 3. Optional Obsidian mirror
if [ -n "$OBSIDIAN_VAULT" ]; then
  OBSIDIAN_PROJECT="$OBSIDIAN_VAULT/Projects/$REPO_NAME"
  mkdir -p "$OBSIDIAN_PROJECT/handoff-archive"
  if [ -f "$MEMORY_ROOT/handoff-archive/$TIMESTAMP.md" ]; then
    cp "$MEMORY_ROOT/handoff-archive/$TIMESTAMP.md" "$OBSIDIAN_PROJECT/handoff-archive/$TIMESTAMP.md"
  fi
  cp "$REPO_ROOT/HANDOFF.md" "$OBSIDIAN_PROJECT/HANDOFF.md"
fi

# 4. Update CODEBASE.md if "Codebase Updates" non-empty
# 5. Update "$MEMORY_ROOT/FAILURES.md" if "Failed Approaches" non-empty
# 6. Append to "$MEMORY_ROOT/decisions-log.md" (dedup)
# 7. Mirror FAILURES.md / decisions-log.md to Obsidian if configured
# 8. Update PLAN.md progress markers + mutations
```
