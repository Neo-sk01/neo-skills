# neo-skills

The Agentic Context Handoff Workflow — a skill that turns AI coding agents from forgetful assistants into persistent engineering partners.

## The Problem

Every time you start a new chat session with an AI coding agent, it starts from zero. It doesn't know what you built yesterday, what decisions you made, or why you chose one library over another. You end up repeating yourself, re-explaining context, and watching the agent make the same mistakes you already corrected.

## The Solution

This skill gives your agent a structured memory system, a documentation verification habit, and a way of working that treats your project as an ongoing epic rather than a pile of disconnected tasks.

It orchestrates four systems:
- **[Superpowers](https://github.com/obra/superpowers)** — development rhythm (brainstorm → plan → execute → review)
- **[Entire](https://entire.io/)** — session recording, checkpoints, crash recovery
- **[Context7](https://github.com/upstash/context7)** — documentation verification with confidence scoring
- **[Codex](https://github.com/openai/codex)** — cross-model review, parallel debugging

## How It Works

**Before the first session**, the agent maps your entire codebase and writes `CODEBASE.md` — its long-term memory of your project.

**At the start of each feature**, the agent frames the work as an epic, cross-references against live library docs via Context7, and builds a verified plan.

**During each session**, the agent works through the plan, validates code against current documentation, and tracks every decision.

**At the end of each session**, the agent writes `HANDOFF.md` — a structured document capturing exactly where it stopped, what was verified, and what's next.

**At the start of the next session**, you say "continue" and the agent picks up exactly where it left off.

## Quick Start

### 1. Install the skill

```bash
git clone https://github.com/neosekaleli/neo-skills.git
```

Copy into your project or Claude Code setup:

```bash
# Project-scoped
cp -r neo-skills/skills/context-handoff .claude/skills/context-handoff

# Or global
cp -r neo-skills/skills/context-handoff ~/.claude/skills/context-handoff
```

### 2. Set up Context7

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

Optional: get a free API key at context7.com/dashboard for higher rate limits.

### 3. Set your Obsidian vault path (optional)

```bash
export OBSIDIAN_VAULT=~/path/to/your/vault
```

### 4. Start your first session

> "Read the context-handoff skill. This is a new project — start with Phase 0 (Codebase Understanding)."

### 5. Resume any future session

> "Read HANDOFF.md and continue."

## The Document Stack

| Document | Lifespan | Role |
|---|---|---|
| `CODEBASE.md` | Long-lived | Architecture, conventions, dependency map |
| `EPIC.md` | Per-epic | Business intent + technical approach |
| `PLAN.md` | Per-epic | Tasks + verified API contracts |
| `HANDOFF.md` | Per-session | Session state for continuity |
| `decisions-log.md` | Cumulative | Institutional memory |

## Skill Files

```
skills/context-handoff/
├── SKILL.md              # The workflow definition
├── REFERENCE.md          # Detailed protocols
├── INTEGRATIONS.md       # Context7, Codex, Entire setup
├── HANDOFF-TEMPLATE.md   # Session handoff template
└── CODEBASE-TEMPLATE.md  # Codebase mapping template
```

See the [onboarding guide](docs/onboarding-guide.md) for a detailed walkthrough.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to contribute.

## License

[MIT](LICENSE)
