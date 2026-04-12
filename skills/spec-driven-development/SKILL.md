---
name: spec-driven-development
description: Produces formal specifications with acceptance criteria before any code is written. Use when starting a new feature, project, or significant change.
---

# Spec-Driven Development

## Overview

Define what you're building — with acceptance criteria — before writing a single line of code. A spec is a contract between the human (what and why) and the agent (how). Code without a spec is guessing.

## When to Use

- Starting a new feature, project, or significant change
- After `idea-refine` has locked a concept
- When requirements exist but aren't formalized
- Before any implementation work

## When NOT to Use

- Bug fixes with clear reproduction steps — go to `debugging-and-error-recovery`
- Trivial changes (rename, config tweak) — just do them
- You already have an approved spec — go to `planning-and-task-breakdown`

## Integration Points

**Context7:** Before committing to any library or API in the spec, verify it supports the required behavior. Run `resolve-library-id` + `query-docs` with specific queries (e.g., "Prisma findMany with nested include and pagination" not just "Prisma query"). Record confidence level.

**Superpowers:** This maps to the design phase of brainstorming. The output feeds into `planning-and-task-breakdown`.

## Core Process

### 1. Gather requirements

Read existing documentation, code, and context. If an `idea-refine` output exists, start from it.

Surface your assumptions:
```
ASSUMPTIONS I'M MAKING:
1. [requirement assumption]
2. [scope assumption]
3. [technical assumption]
→ Correct me now or I'll proceed with these.
```

### 2. Context7 feasibility check

For every library or API the spec will depend on:
1. `resolve-library-id(libraryName)` — get the Context7 ID
2. `query-docs(libraryId, specificQuery)` — verify the capability exists
3. Record: library, query, finding, installed version, confidence level

If confidence is LOW or NONE, flag it in the spec as a risk.

### 3. Write the spec

Structure:

```markdown
# [Feature Name] Specification

## Problem
What problem does this solve? Who has it? Why does it matter?

## Solution
What are we building? High-level description.

## Acceptance Criteria
Testable conditions that define "done":
- [ ] Given [context], when [action], then [result]
- [ ] Given [context], when [action], then [result]

## Non-Goals
What is explicitly OUT of scope.

## Technical Approach
Architecture, key decisions, libraries.

## Risks
What could go wrong. Include any LOW/NONE Context7 findings.

## Context7 Verifications
| Library | Query | Finding | Confidence |
|---------|-------|---------|------------|
```

### 4. Review with user

Present the spec. Ask:
- "Does this match what you have in mind?"
- "Anything missing from acceptance criteria?"
- "Anything in scope that shouldn't be?"

### 5. Lock the spec

Once approved, the spec is the contract. Changes go through plan mutation protocol.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "It's obvious what to build" | If it's obvious, the spec takes 5 minutes. If it's not, you just saved hours. |
| "The spec will slow us down" | Wrong specs are what slow you down. Writing one is fast. |
| "I'll define it as I go" | That's called scope creep. It's the #1 project killer. |
| "Requirements are clear from the code" | Code shows what IS, not what SHOULD BE. Specs define the target. |

## Red Flags

- Writing code before the spec is approved
- Acceptance criteria that aren't testable ("it should be fast")
- Missing non-goals section (scope will creep)
- No Context7 verification on library-dependent features
- Spec approved without user review

## Verification

- [ ] Problem statement is clear and specific
- [ ] Acceptance criteria are testable (Given/When/Then or equivalent)
- [ ] Non-goals section exists and is specific
- [ ] Context7 checks run for all library dependencies
- [ ] User has reviewed and approved the spec
- [ ] Spec saved to a file (not just in conversation)
