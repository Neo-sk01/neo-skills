---
name: idea-refine
description: Refines vague ideas into clear concepts through structured divergent and convergent thinking. Use when the user has an idea but hasn't defined what to build yet.
---

# Idea Refine

## Overview

Turn vague ideas into clear, actionable concepts before any specification work begins. This skill uses divergent thinking (explore possibilities) followed by convergent thinking (narrow to the best option) to ensure you're building the right thing.

## When to Use

- User says "I want to build..." or "What if we..." or "I have an idea for..."
- The request is too vague for a spec — you don't know the shape of the solution yet
- Multiple valid interpretations exist and the user hasn't committed to one

## When NOT to Use

- User has a clear spec or detailed requirements — go to `spec-driven-development`
- User is reporting a bug — go to `debugging-and-error-recovery`
- User knows exactly what to build and just needs a plan — go to `planning-and-task-breakdown`

## Integration Points

**Superpowers:** This skill maps to the Superpowers brainstorming phase. The output feeds directly into `spec-driven-development`.

**Context7:** When the idea involves specific libraries or APIs, use Context7 to verify feasibility before committing to an approach.

## Core Process

### 1. Understand the seed

Ask one question at a time. Start with:
- What problem does this solve? (not what does it do — why does it matter?)
- Who is it for?
- What does success look like?

Don't ask all three at once. One at a time, build understanding.

### 2. Diverge — explore the solution space

Generate 2-3 distinct approaches to the problem. Each should be genuinely different, not variations on one idea.

For each approach:
- One sentence: what is it?
- Key tradeoff: what do you gain and what do you give up?
- Feasibility: is this buildable with the current stack? (Context7 check if uncertain)

### 3. Converge — recommend and refine

Lead with your recommendation and explain why. Let the user challenge, redirect, or approve.

If the user pushes back, don't cave immediately — explain your reasoning. If they have better context, adapt. The goal is the best outcome, not agreement.

### 4. Lock the concept

Before exiting, confirm:
- One sentence: what are we building?
- One sentence: who is it for and what problem does it solve?
- One sentence: what is explicitly out of scope?

### 5. Transition to spec

Once the concept is locked, transition to `spec-driven-development` to formalize it.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "The user already knows what they want" | They know the problem. The solution space hasn't been explored. |
| "This is too small to need refinement" | Small ideas with unexamined assumptions cause the most rework. |
| "I can just start building and iterate" | Iterating without direction is expensive. 10 minutes of refinement saves hours. |
| "Let me gather context first" | This skill IS context gathering. Follow it. |

## Red Flags

- Jumping straight to implementation without exploring alternatives
- Asking zero clarifying questions before proposing a solution
- Proposing only one approach (no divergent thinking)
- Not locking the concept before moving to spec

## Verification

- [ ] At least 2 clarifying questions were asked
- [ ] At least 2 genuinely different approaches were explored
- [ ] A recommendation was given with reasoning
- [ ] The concept was locked in 3 sentences (what, who/why, not-scope)
- [ ] User approved the concept before transitioning
