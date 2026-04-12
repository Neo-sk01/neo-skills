# AGENTS.md

This file provides guidance to AI coding agents working with code in this repository.

## Repository Overview

neo-skills is a collection of production-grade engineering skills for AI coding agents. Skills are structured workflows — not reference docs — that enforce verification at every step. The collection is opinionated toward a four-tool integration stack: Superpowers, Entire, Context7, and Codex.

## Core Rules

- If a task matches a skill, you MUST invoke it
- Skills are located in `skills/<skill-name>/SKILL.md`
- Never implement directly if a skill applies
- Follow skill instructions exactly — do not partially apply them
- Every skill has a verification step. "Seems right" is never sufficient.

## Intent to Skill Mapping

Map user intent to skills automatically:

- Vague idea / brainstorming → `idea-refine`
- Feature / new functionality → `spec-driven-development`, then `incremental-implementation`, `test-driven-development`
- Planning / breakdown → `planning-and-task-breakdown`
- Bug / failure / unexpected behavior → `debugging-and-error-recovery`
- Code review → `code-review`
- Refactoring / simplification → `code-simplification`
- UI work → `frontend-ui-engineering`
- Context management → `context-engineering`
- Deploying / launching → `shipping-and-launch`
- Session ending / handoff → `context-handoff`

## Lifecycle Sequence

For a complete feature:

```
1. idea-refine                 → Refine vague ideas
2. spec-driven-development     → Define what to build
3. planning-and-task-breakdown → Break into verifiable tasks
4. context-engineering         → Load the right context
5. incremental-implementation  → Build slice by slice
6. test-driven-development     → Prove each slice works
7. code-review                 → Review before merge
8. shipping-and-launch         → Deploy safely
9. context-handoff             → Archive session for continuity
```

Not every task needs every skill. A bug fix might only need: `debugging-and-error-recovery` → `test-driven-development` → `code-review`.

## Anti-Rationalization

These thoughts are incorrect:

| Thought | Reality |
|---------|---------|
| "This is too small for a skill" | Small tasks are where unexamined assumptions cause the most waste |
| "I can just quickly implement this" | Quick implementation without process is the top failure mode |
| "I'll gather context first" | Skills tell you HOW to gather context — check first |
| "I know how to do this" | Knowing the concept is not the same as following the workflow |

## Creating a New Skill

See `docs/skill-anatomy.md` for the required format. Every skill must include:
- YAML frontmatter with `name` and `description`
- Overview, When to Use, Integration Points, Core Process
- Common Rationalizations table
- Red Flags list
- Verification checklist
