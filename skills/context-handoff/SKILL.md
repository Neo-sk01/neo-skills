# Context Handoff Skill

Continuity layer for agentic coding sessions. Orchestrates four systems — **Superpowers** (dev rhythm), **Entire** (session recording), **Context7** (doc verification), **Codex** (cross-model review) — managing memory across sessions so nothing is lost.

**Design principle: context is the bottleneck, not speed.**

> Detailed protocols → `REFERENCE.md`
> Integration specifics → `INTEGRATIONS.md`  
> Handoff template → `HANDOFF-TEMPLATE.md`
> Codebase template → `CODEBASE-TEMPLATE.md`

---

## Responsibility Matrix

| Humans own | Agent delivers |
|---|---|
| Strategy — what to build and why | Codebase understanding (CODEBASE.md) |
| Design validation — approve approach | Epic framing (EPIC.md) |
| Scope — what's in/out | Technical plan (PLAN.md) |
| Plan approval + mutation approval (>3 tasks) | Code generation + TDD tests |
| Refinement — edge cases, domain logic | Integration validation (full suite) |
| Final review — quality, standards | Documentation (matching conventions) |
| Deploy — when, how, rollback plan | Context management — handoffs, decisions, failures |
| Human tasks — credentials, vendor, visual QA | Doc verification — Context7 with confidence |

**The human shapes "what" and "why." The agent handles "how" and proves it works.**

---

## Pipeline (Superpowers + Integration Points)

```
brainstorming → design approved
  → git-worktree → branch ready
    → writing-plans → plan approved
      → [subagent-driven | executing-plans]
        → per task: TDD (RED → GREEN → REFACTOR → commit†)
        → if 3 fails: ROLLBACK ⟲ (see REFERENCE.md)
        → after batch: code-review + /codex:adversarial-review ‡
                       + INTEGRATION CHECKPOINT ⬡
        → if bug: systematic-debugging + /codex:rescue §
        → if plan wrong: PLAN MUTATION ∆ (see REFERENCE.md)
        → if human-only: queue to HUMAN TASKS
      → all tasks done
    → verification-before-completion
    → integration checkpoint ⬡ + /codex:review --base main ‡
    → auto-documentation ◈
  → finishing-a-development-branch

† Entire checkpoint on every commit
‡ Codex: cross-model review (high-risk code only)
§ Codex: background delegation
⟲ Rollback protocol (REFERENCE.md)
∆ Plan mutation protocol (REFERENCE.md)
⬡ Integration checkpoint (REFERENCE.md)
◈ Auto-docs at epic completion (REFERENCE.md)
```

---

## Four Phases

### Phase 0: Codebase understanding (first session only)
Map architecture, conventions, domain knowledge. Build cross-system dependency map (see `examples/CODEBASE-TEMPLATE.md`). Run Context7 dependency audit with confidence scoring. Capture lockfile hash + test baseline.

### Phase 1: Epic framing
Brainstorming activates. Cross-reference design against CODEBASE.md dependency map for feasibility + FAILURES.md to avoid dead ends. Context7 feasibility check. Identify human tasks early. Produce EPIC.md.

### Phase 2: Plan design
Worktree + plan generated. Mark handoff-safe boundaries (`--- HANDOFF SAFE ---`), human-required tasks, and integration checkpoint points. Context7 verification on all library methods. Record in PLAN.md.

### Phase 3: Session lifecycle

**Resume:** Load HANDOFF.md + CODEBASE.md + PLAN.md + FAILURES.md. Preflight: commit match, test manifest (specific regressions), lockfile hash (dependency changes), `/codex:status` (pending jobs), human tasks (resolved?), LOW/NONE doc-checks. Estimate context budget.

**Work:** Superpowers drives. Monitor: context budget thresholds (see REFERENCE.md), rollback protocol (3 fails → revert), plan mutation (formal), integration checkpoints (full suite after batches), Context7 at TDD RED, Codex for high-risk review + delegation, human task queuing.

**Handoff:** Push to next safe point → check Codex jobs → verification → compression pass → generate HANDOFF.md (see `HANDOFF-TEMPLATE.md`) → archive first, then save to Git + Obsidian.

### Phase 4: Epic completion
Full integration checkpoint. Codex review on full diff. Auto-documentation (README, API docs, migration guides, inline comments). Remaining human tasks defined. Superpowers finishes branch. Archive everything to Obsidian.

---

## Handoff Timing Map

| Position | Quality | Rule |
|---|---|---|
| After brainstorming/plan approval | ✅ CLEAN | Always hand off here |
| Between tasks (after commit) | ✅ CLEAN | Always safe |
| After review + integration checkpoint | ✅ CLEAN | Always safe |
| After GREEN (before refactor) | ⚠️ OK | If budget is CAUTION |
| Mid-RED | ⚠️ OK | Capture test path |
| Mid-brainstorming/debugging/review | ❌ AVOID | Only if critically low budget |
| Codex job running | ⚠️ OK | Record job ID |

**Budget overrides timing.** CAUTION (<5k tokens/task) → finish current, evaluate. HANDOFF NOW (<2k) → stop at next ⚠️/✅.

---

## Document Stack

| Document | Lifespan | Role |
|---|---|---|
| `CODEBASE.md` | Long-lived | Architecture, conventions, dependency map |
| `EPIC.md` | Per-epic | Business intent + human tasks |
| `PLAN.md` | Per-epic | Tasks + APIs + mutations + integration markers |
| `FAILURES.md` | Cumulative | Failed approaches — prevents dead ends |
| `HANDOFF.md` | Per-session | Pipeline position + budget + all refs |
| `decisions-log.md` | Cumulative | Decisions + bugs + Codex patterns |
| Entire checkpoints | Permanent | Full transcripts tied to commits |
| Handoff archive | Permanent | Timestamped session history |

---

## Anti-Patterns

- Don't reproduce transcripts — Entire has them
- Don't hand off mid-brainstorming or mid-debugging
- Don't retry failed approaches — read FAILURES.md
- Don't dig into dead ends — rollback after 3 fails
- Don't silently change the plan — mutation protocol
- Don't silently skip tasks — queue human tasks with reason
- Don't skip integration checkpoints — per-task TDD ≠ system validation
- Don't skip compression pass before handoff
- Don't treat all doc-checks as equal — LOW = re-check
- Don't ship without documentation
- Don't archive after overwriting — archive first
- Don't guess feasibility — dependency map + Context7

---

## Recovery

| Scenario | Path |
|---|---|
| Lost HANDOFF.md | `entire explain <commit>` + `/codex:status` |
| Session crash | Entire shadow branch + Obsidian archive |
| Repeated dead end | FAILURES.md |
| Pending Codex job | `/codex:status` → `/codex:result` |
| Dependency changed | Lockfile hash → Context7 |
| Cross-system break | Integration checkpoint → dependency map |
| Human task blocking | Queue → user resolves → next session unblocks |
