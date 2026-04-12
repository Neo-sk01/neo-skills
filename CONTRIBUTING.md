# Contributing to neo-skills

## Improving the Context Handoff Skill

The context-handoff skill lives in `skills/context-handoff/` with five files:

| File | Purpose |
|------|---------|
| `SKILL.md` | Main workflow definition |
| `REFERENCE.md` | Detailed protocols (rollback, plan mutation, integration checkpoints) |
| `INTEGRATIONS.md` | Context7, Codex, and Entire configuration and usage |
| `HANDOFF-TEMPLATE.md` | Template for session handoff documents |
| `CODEBASE-TEMPLATE.md` | Template for codebase mapping |

### Making Changes

- Keep `SKILL.md` under 500 lines — detailed protocols belong in `REFERENCE.md`
- If changing the workflow, update the corresponding sections in `REFERENCE.md` and templates
- Test changes by running through a full session lifecycle (resume → work → handoff)

### Style Guidelines

- The skill is a workflow, not a reference doc — tell agents what to DO
- Be specific and concrete — "generate HANDOFF.md using the template" not "document the session"
- Verification requires evidence — "seems right" is never sufficient

### Submit a PR

- Describe what you changed and why
- Include an example of how the change affects the workflow
- If changing templates, show a before/after example
