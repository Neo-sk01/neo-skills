---
name: context-handoff
description: "Multi-session continuity and context management for agentic coding workflows. Use this skill when the user's intent involves cross-session coordination — resuming from a previous session, loading or generating a HANDOFF.md, running out of context window or budget, mapping a codebase for the first time (Phase 0), planning work that spans multiple sessions, framing an epic for multi-session execution, tracking failed approaches across sessions, handing off a project to another developer or agent, checking Entire status or checkpoints, rolling back to a previous checkpoint, creating a multi-session plan, or recovering lost context between sessions. Also triggers on explicit continuity concepts: session handoffs, context budget, reload packets, Entire archaeology, compact reload packets, or session lifecycle management. Do NOT trigger on mere filename mentions (CODEBASE.md, EPIC.md, PLAN.md, FAILURES.md) unless the surrounding intent is clearly about session continuity, handoff generation, or cross-session coordination — editing, reviewing, or summarizing those files is not a trigger."
---

# Context Handoff Skill

Continuity layer for agentic coding sessions. Orchestrates four systems — **Superpowers** (dev rhythm), **Entire** (session recording), **Context7** (doc verification), **Codex** (cross-model review) — managing memory across sessions so nothing is lost.

**Design principle: context is the bottleneck, not speed.**

**Fresh-context contract:** every session must leave behind a compact reload packet, verification evidence, enough token/context-budget data for the next session to stay fresh, and precise Entire archaeology targets for recovering full context without blindly replaying every transcript.

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
Map architecture, conventions, domain knowledge. Build cross-system dependency map (see `CODEBASE-TEMPLATE.md`). Run Context7 dependency audit with confidence scoring. Capture lockfile hash + test baseline.

### Phase 1: Epic framing
Brainstorming activates. Cross-reference design against CODEBASE.md dependency map for feasibility + FAILURES.md (or repo-local memory fallback) to avoid dead ends. Context7 feasibility check. Identify human tasks early. Produce EPIC.md.

### Phase 2: Plan design
Worktree + plan generated. Mark handoff-safe boundaries (`--- HANDOFF SAFE ---`), human-required tasks, and integration checkpoint points. Context7 verification on all library methods. Record in PLAN.md.

### Phase 3: Session lifecycle

**Resume:** Load `HANDOFF.md` first. Run `entire status` to confirm repository/session health. Use the handoff's compact reload packet to decide which supporting docs to load next and which Entire checkpoints/transcript segments to inspect next. Resume in layers: handoff first, targeted docs second, targeted Entire archaeology third. Default archaeology path: `entire explain --short` first, then `--full` or `--raw-transcript` only where the handoff points you. Use `entire resume <branch>` when restoring work on an existing branch and `entire doctor` if local session state is stuck or stale.

**Work:** Superpowers drives. Monitor context usage continuously (see REFERENCE.md): check it at session start, after large reload/tool outputs, after each task, and before starting a new task. When context used reaches 150,000 tokens, enter HANDOFF NOW: finish the smallest safe atomic unit, then generate `HANDOFF.md` before taking on more work. Also monitor rollback protocol (3 fails → safe restore or fresh worktree, never destructive reset), plan mutation (formal), integration checkpoints (full suite after batches), Context7 at TDD RED, Codex for high-risk review + delegation, human task queuing, and the projected size of the next session's reload packet plus its targeted Entire archaeology path. Prefer logical commits at meaningful boundaries so Entire checkpoints stay useful for rewind and archaeology. Use `entire rewind` only for intentional active-session rollback inside the current session; use `entire resume <branch>` for later re-entry.

**Handoff:** Push to next safe point → check Codex jobs → verification → compression pass → generate `HANDOFF.md` (see `HANDOFF-TEMPLATE.md`) with a compact reload packet, context-budget data, and explicit Entire archaeology targets → archive the previous handoff in repo-local memory first → save current handoff to Git → mirror to Obsidian only if configured. If the host offers a built-in compact/summarize feature, run it only after the handoff is written and verified.

### Phase 4: Epic completion
Full integration checkpoint. Codex review on full diff. Auto-documentation (README, API docs, migration guides, inline comments). Remaining human tasks defined. Superpowers finishes branch. Archive everything to repo-local memory and mirror to Obsidian if configured.

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
| Context used ≥150k tokens | ⚠️/✅ | HANDOFF NOW at the next safe atomic stop |

**Budget overrides timing.** Context used ≥150k tokens → HANDOFF NOW. Otherwise CAUTION (<5k tokens/task) → finish current, evaluate. HANDOFF NOW (<2k) → stop at next ⚠️/✅.

---

## Document Stack

| Document | Lifespan | Role |
|---|---|---|
| `CODEBASE.md` | Long-lived | Architecture, conventions, dependency map |
| `EPIC.md` | Per-epic | Business intent + human tasks |
| `PLAN.md` | Per-epic | Tasks + APIs + mutations + integration markers |
| `FAILURES.md` | Cumulative | Failed approaches — prevents dead ends |
| `HANDOFF.md` | Per-session | Compact reload packet + pipeline position + budget + refs |
| `decisions-log.md` | Cumulative | Decisions + bugs + Codex patterns |
| Entire checkpoints | Permanent | Full transcripts tied to commits |
| Handoff archive | Permanent | Timestamped session history |

**Persistence rule:** Git repo is always the canonical handoff store. Repo-local memory (`.agent-memory/`) is always available for archives and cumulative logs. Obsidian is an optional mirror, not a required dependency.

---

## Anti-Patterns

- Don't reproduce transcripts — Entire has them
- Don't blindly replay the whole transcript when `HANDOFF.md` can route you to the right Entire checkpoints and transcript segments
- Don't hand off mid-brainstorming or mid-debugging
- Don't retry failed approaches — read FAILURES.md
- Don't dig into dead ends — rollback after 3 fails
- Don't use destructive rollback commands in a dirty tree
- Don't silently change the plan — mutation protocol
- Don't silently skip tasks — queue human tasks with reason
- Don't skip integration checkpoints — per-task TDD ≠ system validation
- Don't skip compression pass before handoff
- Don't let the handoff bloat until it defeats clean-context resumption
- Don't omit the Entire archaeology targets that the next session will need for full-context recovery
- Don't omit token/context-budget data from the handoff
- Don't treat all doc-checks as equal — LOW = re-check
- Don't ship without documentation
- Don't archive after overwriting — archive first
- Don't guess feasibility — dependency map + Context7

---

## Recovery

| Scenario | Path |
|---|---|
| Lost HANDOFF.md | `entire explain <commit>` + `/codex:status` |
| Session crash | Entire shadow branch + repo-local handoff archive (+ Obsidian mirror if configured) |
| Repeated dead end | FAILURES.md |
| Pending Codex job | `/codex:status` → `/codex:result` |
| Dependency changed | Lockfile hash → Context7 |
| Cross-system break | Integration checkpoint → dependency map |
| Human task blocking | Queue → user resolves → next session unblocks |
