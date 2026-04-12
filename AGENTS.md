# AGENTS.md

This file provides guidance to AI coding agents working with code in this repository.

## Repository Overview

neo-skills contains the Context Handoff skill — a continuity layer for agentic coding sessions. It orchestrates Superpowers, Entire, Context7, and Codex to manage memory across sessions so nothing is lost.

## The Skill

The context-handoff skill is located at `skills/context-handoff/SKILL.md`. Read it and follow its workflow when:

- Starting a new project (Phase 0 — Codebase Understanding)
- Beginning a new feature (Phase 1 — Epic Framing)
- Planning implementation (Phase 2 — Plan Design)
- Working through a session (Phase 3 — Session Lifecycle)
- Ending a session or hitting context pressure (Handoff generation)
- Resuming from a previous session (Preflight check + continue)

## Core Rules

- Follow the skill workflow exactly — do not partially apply it
- Every doc-check via Context7 is mandatory, not optional
- Handoff documents must be generated at every session end — no exceptions
- Archive previous handoffs in repo-local memory before overwriting the current one
- "Seems right" is never sufficient — verification requires evidence
- Resume from `HANDOFF.md`, run `entire status`, then use the Compact Reload Packet and referenced Entire checkpoints for deeper transcript archaeology

## Document Stack

| Document | Lifespan | Role |
|---|---|---|
| `CODEBASE.md` | Long-lived | Architecture, conventions, dependency map |
| `EPIC.md` | Per-epic | Business intent + human tasks |
| `PLAN.md` | Per-epic | Tasks + APIs + mutations + integration markers |
| `HANDOFF.md` | Per-session | Compact reload packet + pipeline position + budget + refs |
| `decisions-log.md` | Cumulative | Decisions + bugs + patterns |
