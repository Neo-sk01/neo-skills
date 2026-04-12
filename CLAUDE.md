# neo-skills

The Agentic Context Handoff Workflow — a skill that turns AI coding agents from forgetful assistants into persistent engineering partners.

## The Skill

The context-handoff skill lives at `skills/context-handoff/SKILL.md` with supporting files:

- `SKILL.md` — the workflow definition
- `REFERENCE.md` — detailed protocols (rollback, plan mutation, integration checkpoints)
- `INTEGRATIONS.md` — Context7, Codex, and Entire setup and usage
- `HANDOFF-TEMPLATE.md` — session handoff document template
- `CODEBASE-TEMPLATE.md` — codebase mapping template

## How It Works

The skill orchestrates four systems — Superpowers (dev rhythm), Entire (session recording), Context7 (doc verification), Codex (cross-model review) — managing memory across sessions so nothing is lost.

**Design principle: context is the bottleneck, not speed.**

## The Four Phases

1. **Phase 0 — Codebase Understanding:** Map architecture, conventions, dependencies. Build `CODEBASE.md`.
2. **Phase 1 — Epic Framing:** Define what to build, cross-reference against CODEBASE.md, produce `EPIC.md`.
3. **Phase 2 — Plan Design:** Break epic into tasks with verified API contracts. Produce `PLAN.md`.
4. **Phase 3 — Session Lifecycle:** Resume from handoff, work the plan, generate `HANDOFF.md` at session end.

## Quick Reference

| I want to... | Do this |
|---|---|
| Start a new project | "Start with Phase 0 — map this codebase" |
| Begin a new feature | "Frame an epic for {feature description}" |
| Continue work | Drop `HANDOFF.md` → "Read HANDOFF.md and continue" |
| Force a handoff | "Wrap up and generate a handoff" |
| Check a library's docs | "Use Context7 to verify {library} {method}" |

## Integration Stack

- **Superpowers** — development rhythm: brainstorming → planning → execution → review → finishing
- **Entire** — session recording: checkpoints on every commit, full transcripts, crash recovery
- **Context7** — documentation verification: resolve library IDs, query current docs, confidence scoring (HIGH/MEDIUM/LOW/NONE)
- **Codex** — cross-model review: adversarial review on high-risk code, parallel debugging, background delegation
