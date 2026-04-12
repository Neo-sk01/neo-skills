---
name: code-simplification
description: Reduces code complexity by removing unnecessary abstractions and indirection. Use when code is harder to understand than the problem it solves.
---

# Code Simplification

## Overview

Complexity is a cost. Every abstraction, layer, and indirection must earn its place. This skill identifies unnecessary complexity and removes it while preserving behavior. The goal is code that's as simple as the problem allows — no simpler.

## When to Use

- Code is harder to read than the problem it solves
- After a feature is complete and working (simplify as a separate step)
- When reviewing code that feels over-engineered
- When a file has grown too large and does too many things

## When NOT to Use

- During initial implementation — get it working first, then simplify
- When you don't understand the code — understand before changing
- Chesterton's Fence: if you don't know why something exists, don't remove it

## Core Process

### 1. Understand before changing

Read the code. Understand what it does and why. If anything is unclear, investigate — check git blame, read tests, check for callers.

Never simplify code you don't fully understand.

### 2. Identify complexity

Look for:
- **Dead code** — unreachable branches, unused functions, commented-out blocks
- **Unnecessary abstraction** — interfaces with one implementation, base classes with one child, factories that create one thing
- **Premature generalization** — config options nobody uses, plugin systems with no plugins, generic code for one use case
- **Indirection without value** — wrapper functions that just call another function, delegation chains
- **Feature flags for shipped features** — clean them up

### 3. Apply Chesterton's Fence

Before removing anything:
- Why was this added? (git blame + commit message)
- Is it still needed? (grep for callers/references)
- If you can't determine why, don't remove it — ask

### 4. Simplify

One change at a time:
1. Remove the simplest unnecessary thing
2. Run tests — all must pass
3. Commit
4. Repeat

Never simplify multiple things in one commit. If a simplification breaks tests, you need to know exactly which change caused it.

### 5. Verify behavior preserved

After all simplifications:
- All existing tests pass without modification
- No new behavior added (simplification is not enhancement)
- The code does exactly what it did before, just more simply

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "We might need this flexibility later" | YAGNI. Remove it. Add it back when you actually need it. |
| "The abstraction makes it cleaner" | Abstraction with one consumer is not cleaner — it's indirection. |
| "I'll just clean up everything while I'm here" | Scope discipline. Simplify the code you're working on, not adjacent code. |
| "This refactor is too risky without tests" | Then write the tests first. Don't leave complexity because testing is hard. |

## Red Flags

- Simplifying code you don't understand
- Removing code without checking for callers
- Multiple simplifications in one commit
- Tests modified during simplification (behavior changed)
- Scope expanding beyond the target code

## Verification

- [ ] Code was fully understood before changes
- [ ] Chesterton's Fence checked for every removal
- [ ] Each simplification is in its own commit
- [ ] All existing tests pass without modification
- [ ] No behavior change (simplification, not enhancement)
- [ ] Code is genuinely simpler (fewer lines, fewer abstractions, fewer indirections)
