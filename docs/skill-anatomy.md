# Skill Anatomy

How to write a new skill for neo-skills. Every skill follows the same structure.

## Directory Structure

```
skills/
  skill-name/         # kebab-case directory name
    SKILL.md           # Required: skill definition
    REFERENCE.md       # Optional: detailed reference material
    INTEGRATIONS.md    # Optional: integration-specific details
```

## SKILL.md Format

Every SKILL.md requires YAML frontmatter and these sections:

```markdown
---
name: skill-name
description: One sentence. What it does + when to trigger it.
---

# Skill Title

## Overview
2-3 sentences. What this skill does and why it matters.

## When to Use
- Specific triggering conditions
- Be concrete: "when the user says X" or "when Y is true"

## When NOT to Use
- Exclusions with redirects to the correct skill

## Integration Points
How this skill connects to Superpowers, Entire, Context7, and/or Codex.
Only include integrations that are relevant to this skill.

## Core Process
Numbered step-by-step workflow. This is the meat of the skill.
Each step must be actionable — not advice, but instructions.
Include verification gates: no step is "done" until evidence proves it.

## Common Rationalizations
| Rationalization | Reality |
|----------------|---------|
| "Agent excuse" | Why the excuse is wrong |

This table prevents agents from skipping steps. Include 4-6 entries
covering the most common shortcuts an agent might take.

## Red Flags
Observable signs the skill isn't being followed. Bullet list.

## Verification
Checklist with evidence requirements. Every item should be
verifiable with output, not just "seems right."
```

## Key Principles

### Process over knowledge

Skills are workflows, not reference docs. Every section should tell the agent what to DO, not what to KNOW. Reference material belongs in supporting files or the `references/` directory.

### Under 500 lines

Keep SKILL.md under 500 lines. If reference material exceeds ~100 lines, put it in a supporting file (REFERENCE.md, INTEGRATIONS.md) and link to it from SKILL.md.

### Specific descriptions

The `description` field in frontmatter is how agents decide whether to activate a skill. It must communicate both WHAT the skill does and WHEN to use it. Bad: "Helps with testing." Good: "Enforces RED-GREEN-REFACTOR cycle — failing test first, then minimal implementation, then cleanup. Use when writing or running tests."

### Common Rationalizations are essential

This is the most important innovation from agent-skills. Agents will rationalize skipping steps. The rationalizations table preempts this by pairing excuses with counter-arguments. Write entries based on actual observed agent behavior.

### Verification requires evidence

"Seems right" is never sufficient. Every verification item should be checkable with concrete evidence: test output, file existence, command output, git log.

## Supporting Files

Use supporting files when:
- Reference material exceeds ~100 lines
- Integration details are complex enough to need their own file
- Templates or examples are large

Naming: UPPERCASE.md (REFERENCE.md, INTEGRATIONS.md, TEMPLATE.md)

## Testing a Skill

1. Create a test project with a task that should trigger the skill
2. Verify the skill activates when expected
3. Follow the skill workflow completely
4. Check that verification criteria can be objectively assessed
5. Have someone unfamiliar with the skill try to follow it — if they get stuck, the skill needs clarification
