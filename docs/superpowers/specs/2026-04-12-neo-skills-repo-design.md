# neo-skills Repository Design

## Summary

Open-source, Claude Code-targeted collection of 12 production-grade engineering skills integrated with Superpowers, Entire, Context7, and Codex. Follows the structure of [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) but with fewer, deeper skills that reflect a specific toolchain and workflow.

## Repository Structure

```
neo-skills/
├── .claude/
│   └── commands/
│       ├── spec.md
│       ├── plan.md
│       ├── build.md
│       ├── test.md
│       ├── review.md
│       ├── debug.md
│       └── ship.md
├── agents/
│   ├── code-reviewer.md
│   ├── test-engineer.md
│   └── security-auditor.md
├── docs/
│   ├── getting-started.md
│   └── skill-anatomy.md
├── references/
│   ├── testing-patterns.md
│   ├── security-checklist.md
│   └── performance-checklist.md
├── skills/
│   ├── idea-refine/
│   │   └── SKILL.md
│   ├── spec-driven-development/
│   │   └── SKILL.md
│   ├── planning-and-task-breakdown/
│   │   └── SKILL.md
│   ├── incremental-implementation/
│   │   └── SKILL.md
│   ├── test-driven-development/
│   │   └── SKILL.md
│   ├── debugging-and-error-recovery/
│   │   └── SKILL.md
│   ├── code-review/
│   │   └── SKILL.md
│   ├── code-simplification/
│   │   └── SKILL.md
│   ├── context-handoff/
│   │   ├── SKILL.md
│   │   ├── REFERENCE.md
│   │   ├── INTEGRATIONS.md
│   │   ├── HANDOFF-TEMPLATE.md
│   │   └── CODEBASE-TEMPLATE.md
│   ├── context-engineering/
│   │   └── SKILL.md
│   ├── frontend-ui-engineering/
│   │   └── SKILL.md
│   └── shipping-and-launch/
│       └── SKILL.md
├── CLAUDE.md
├── AGENTS.md
├── README.md
├── LICENSE
├── CONTRIBUTING.md
└── .gitignore
```

## Skills Catalog

12 skills organized by SDLC phase:

| Phase | Skill | Integration Points | Differentiator vs Osmani |
|-------|-------|--------------------|--------------------------|
| Define | `idea-refine` | Superpowers brainstorming | Feeds into Superpowers pipeline |
| Define | `spec-driven-development` | Context7 | Feasibility checks via Context7 |
| Plan | `planning-and-task-breakdown` | Entire, Superpowers | Handoff-safe boundaries, checkpoint markers |
| Build | `incremental-implementation` | Entire, Context7 | Context budget tracking, plan mutation protocol |
| Build | `test-driven-development` | Context7, Codex | Context7 at RED, Codex rescue on 3 fails |
| Build | `context-engineering` | Context7 | Integrated with CODEBASE.md template |
| Build | `frontend-ui-engineering` | — | Adapted to neo-skills conventions |
| Verify | `debugging-and-error-recovery` | Codex, Entire | Rollback protocol + Codex parallel investigation |
| Review | `code-review` | Codex | Adversarial review on high-risk code |
| Review | `code-simplification` | — | Neo-skills conventions |
| Ship | `context-handoff` | All four | Crown jewel — session continuity layer |
| Ship | `shipping-and-launch` | Entire, Codex | Integration checkpoints, Entire archival |

## Skill Format

Every SKILL.md follows this anatomy:

```markdown
---
name: skill-name
description: One sentence. What it does + when to trigger it.
---

# Skill Title

## Overview
2-3 sentences. Elevator pitch.

## When to Use
- Triggering conditions
- Exclusions (when NOT to use)

## Integration Points
How this skill connects to Superpowers, Entire, Context7, and/or Codex.
(Only relevant integrations — not all four every time.)

## Core Process
Numbered step-by-step workflow.
Includes verification gates — no step is "done" until evidence proves it.

## Common Rationalizations
| Rationalization | Reality |
|----------------|---------|
| "This is too simple for X" | Counter-argument |

## Red Flags
Observable signs the skill isn't being followed.

## Verification
Checklist with evidence requirements.
```

Skills stay under 500 lines. Heavy reference material goes in supporting files.

## CLAUDE.md

Project-level rules:
- Skill discovery before any work
- Skills are workflows, not suggestions
- Core operating behaviors: surface assumptions, manage confusion, push back, enforce simplicity, scope discipline, verify don't assume
- Integration stack overview

## Slash Commands

7 commands mapping to skill chains:

| Command | Maps To |
|---------|---------|
| `/spec` | `idea-refine` → `spec-driven-development` |
| `/plan` | `planning-and-task-breakdown` |
| `/build` | `incremental-implementation` |
| `/test` | `test-driven-development` |
| `/review` | `code-review` + Codex adversarial |
| `/debug` | `debugging-and-error-recovery` |
| `/ship` | `shipping-and-launch` + `context-handoff` |

## Agent Personas

3 specialist personas for review:
- **code-reviewer.md** — Senior Staff Engineer: architecture, maintainability
- **test-engineer.md** — QA Specialist: coverage, edge cases, test quality
- **security-auditor.md** — Security Engineer: OWASP, auth, secrets

## References

3 lookup checklists (not skills):
- `testing-patterns.md` — test structure, naming, mocking
- `security-checklist.md` — OWASP, auth, headers, validation
- `performance-checklist.md` — Core Web Vitals, profiling

## Docs

2 guides:
- `getting-started.md` — installation and setup for Claude Code
- `skill-anatomy.md` — how to write a new skill (for contributors)

## Supporting Files

- `README.md` — project overview, skill catalog, install, commands
- `AGENTS.md` — AI agent guidance, skill discovery flow, intent mapping
- `CONTRIBUTING.md` — how to add skills, format, PR expectations
- `LICENSE` — MIT
- `.gitignore` — .DS_Store, node_modules, .env

## Design Decisions

| Decision | Rationale |
|----------|-----------|
| 12 skills, not 20 | Depth over breadth. Every skill has real integration content, no filler. |
| Opinionated toward 4-tool stack | Differentiator. Generic skills already exist in Osmani's repo. |
| Claude Code only | User's primary platform. Avoids maintaining docs for platforms not used. |
| Context-handoff as-is | Already production-quality. Move into `skills/` directory unchanged. |
| Osmani's format (rationalizations, red flags) | Best innovation in that repo. Prevents agents from skipping steps. |
| Supporting files pattern | Proven by context-handoff. Keeps SKILL.md focused, reference material separate. |
