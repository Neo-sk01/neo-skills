# neo-skills

Production-grade engineering skills for AI coding agents, integrated with [Superpowers](https://github.com/anthropics/superpowers), [Entire](https://github.com/anthropics/entire), [Context7](https://context7.com), and [Codex](https://github.com/openai/codex).

## What is this?

A collection of 12 structured engineering workflows for AI coding agents. Each skill encodes a process that senior engineers follow — from refining ideas to shipping features — with verification gates that prevent shortcuts.

Skills are **workflows, not reference docs.** They tell agents what to DO, step by step, with evidence-based verification at every stage.

## Philosophy

- **Context is the bottleneck, not speed.** Skills manage what information enters context and when.
- **Verify, don't assume.** Every skill has a verification step. "Seems right" is never sufficient.
- **Integrated toolchain.** Skills leverage four tools working together: Superpowers (dev rhythm), Entire (session recording), Context7 (doc verification), Codex (cross-model review).

## Skills

| Phase | Skill | What it does |
|-------|-------|-------------|
| **Define** | [idea-refine](skills/idea-refine/SKILL.md) | Refines vague ideas through divergent/convergent thinking |
| **Define** | [spec-driven-development](skills/spec-driven-development/SKILL.md) | Produces specs with acceptance criteria before code |
| **Plan** | [planning-and-task-breakdown](skills/planning-and-task-breakdown/SKILL.md) | Decomposes specs into small, verifiable tasks |
| **Build** | [incremental-implementation](skills/incremental-implementation/SKILL.md) | Builds through thin vertical slices |
| **Build** | [test-driven-development](skills/test-driven-development/SKILL.md) | RED-GREEN-REFACTOR with Codex rescue |
| **Build** | [context-engineering](skills/context-engineering/SKILL.md) | Manages context loading and verification |
| **Build** | [frontend-ui-engineering](skills/frontend-ui-engineering/SKILL.md) | Accessible, responsive UI components |
| **Verify** | [debugging-and-error-recovery](skills/debugging-and-error-recovery/SKILL.md) | Five-step triage with rollback protocol |
| **Review** | [code-review](skills/code-review/SKILL.md) | Five-axis review with Codex adversarial |
| **Review** | [code-simplification](skills/code-simplification/SKILL.md) | Removes unnecessary complexity |
| **Ship** | [context-handoff](skills/context-handoff/SKILL.md) | Session continuity across handoffs |
| **Ship** | [shipping-and-launch](skills/shipping-and-launch/SKILL.md) | Pre-launch checks and deployment |

## Quick Start

### Install

```bash
git clone https://github.com/neosekaleli/neo-skills.git
```

### Use with Claude Code

Reference skills in your project or copy them to your Claude Code setup:

```bash
cp -r neo-skills/skills/* ~/.claude/skills/
```

### Slash Commands

| Command | Maps to |
|---------|---------|
| `/spec` | idea-refine -> spec-driven-development |
| `/plan` | planning-and-task-breakdown |
| `/build` | incremental-implementation |
| `/test` | test-driven-development |
| `/review` | code-review + Codex adversarial |
| `/debug` | debugging-and-error-recovery |
| `/ship` | shipping-and-launch + context-handoff |

See [Getting Started](docs/getting-started.md) for full setup instructions including the integration stack.

## Integration Stack

Each integration is optional but recommended:

| Tool | Purpose | Setup |
|------|---------|-------|
| **Superpowers** | Development rhythm (brainstorm -> plan -> execute -> review) | Claude Code plugin |
| **Entire** | Session recording, checkpoints, crash recovery | `npm i -g @anthropic/entire-cli` |
| **Context7** | Documentation verification with confidence scoring | `claude mcp add context7` |
| **Codex** | Cross-model review, parallel debugging, delegation | `/plugin marketplace add openai/codex-plugin-cc` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add or modify skills.

## License

[MIT](LICENSE)
