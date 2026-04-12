# Contributing to neo-skills

## Adding a New Skill

### 1. Create the skill directory

```bash
mkdir -p skills/your-skill-name
```

Use kebab-case for the directory name.

### 2. Write SKILL.md

Follow the format in [docs/skill-anatomy.md](docs/skill-anatomy.md). Every skill requires:

- YAML frontmatter with `name` and `description`
- Overview (2-3 sentences)
- When to Use / When NOT to Use
- Integration Points (only relevant integrations)
- Core Process (numbered, actionable steps with verification gates)
- Common Rationalizations table (4-6 entries)
- Red Flags (bullet list)
- Verification checklist (evidence-based)

### 3. Keep it under 500 lines

The main SKILL.md should be under 500 lines. Put reference material in supporting files:

- `REFERENCE.md` — detailed protocols or lookup tables
- `INTEGRATIONS.md` — complex integration details
- Templates or examples in their own files

### 4. Test it

1. Create a test project with a task that should trigger your skill
2. Follow the skill workflow completely
3. Verify that every verification criterion can be objectively assessed
4. Check that the Common Rationalizations table addresses real agent shortcuts

### 5. Submit a PR

- One skill per PR
- Include a description of when the skill should be triggered
- Include an example of the skill being used (if possible)

## Modifying an Existing Skill

- Keep changes focused — don't restructure while fixing a bug
- If changing the Core Process, verify the Verification checklist still matches
- Update Common Rationalizations if you've observed new agent shortcuts

## Style Guidelines

- Skills are workflows, not reference docs — tell agents what to DO
- Be specific and concrete — "run `npm test`" not "run tests"
- Verification requires evidence — "seems right" is never sufficient
- Use tables for structured comparisons (rationalizations, severity levels)
- Write in second person imperative ("Write the test", not "The test should be written")

## What NOT to Contribute

- Reference docs that don't encode a workflow — put these in `references/`
- Platform-specific setup — this repo targets Claude Code only
- Skills that duplicate existing skills — extend them instead
