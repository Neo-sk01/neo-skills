# neo-skills

Production-grade engineering skills for AI coding agents, integrated with Superpowers, Entire, Context7, and Codex.

## Skill Rules

- Every skill lives in `skills/<name>/SKILL.md`
- Check for an applicable skill before starting any work — even a 1% chance means check
- Skills are workflows, not suggestions — follow steps in order, don't skip verification
- Multiple skills can chain: idea-refine → spec → plan → build → test → review → ship
- "Seems right" is never sufficient — verification requires evidence (test output, build results, runtime data)

## Core Operating Behaviors

1. **Surface assumptions** — before implementing anything non-trivial, state your assumptions explicitly and ask for correction
2. **Manage confusion actively** — when you hit inconsistencies or unclear specs, STOP. Name the confusion. Ask. Don't guess.
3. **Push back when warranted** — sycophancy is a failure mode. Point out problems, quantify downsides, propose alternatives. Accept override with full information.
4. **Enforce simplicity** — prefer the boring, obvious solution. If 100 lines suffice, 1000 lines is a failure. Ask: "Would a staff engineer say 'why didn't you just...?'"
5. **Maintain scope discipline** — touch only what you're asked to touch. Don't clean up adjacent code, remove comments you don't understand, or add unrequested features.
6. **Verify, don't assume** — every skill has a verification step. A task is not complete until verification passes.

## Integration Stack

- **Superpowers** — development rhythm: brainstorming → planning → execution → review → finishing
- **Entire** — session recording: checkpoints on every commit, full transcripts, crash recovery
- **Context7** — documentation verification: resolve library IDs, query current docs, confidence scoring (HIGH/MEDIUM/LOW/NONE)
- **Codex** — cross-model review: adversarial review on high-risk code, parallel debugging, background delegation

## Skill Discovery

```
Task arrives
    ├── Vague idea? ───────────────→ idea-refine
    ├── New feature/change? ───────→ spec-driven-development
    ├── Have spec, need tasks? ────→ planning-and-task-breakdown
    ├── Implementing? ─────────────→ incremental-implementation
    │   ├── UI work? ──────────────→ frontend-ui-engineering
    │   └── Need context? ─────────→ context-engineering
    ├── Writing tests? ────────────→ test-driven-development
    ├── Something broke? ──────────→ debugging-and-error-recovery
    ├── Reviewing code? ───────────→ code-review
    │   └── Too complex? ──────────→ code-simplification
    ├── Ready to deploy? ──────────→ shipping-and-launch
    └── Session ending? ───────────→ context-handoff
```

## Conventions

- Skill directories: `kebab-case`
- Skill files: `SKILL.md` (always uppercase)
- Supporting files: `UPPERCASE.md` (e.g., `REFERENCE.md`, `INTEGRATIONS.md`)
- YAML frontmatter required on every `SKILL.md`
- Skills stay under 500 lines — heavy reference goes in supporting files
