# neo-skills

The Agentic Context Handoff Workflow — a Claude Code skill that turns AI coding agents from forgetful assistants into persistent engineering partners.

## The Problem

Every time you start a new chat session with an AI coding agent, it starts from zero. It doesn't know what you built yesterday, what decisions you made, or why you chose one library over another. You end up repeating yourself, re-explaining context, and watching the agent make the same mistakes you already corrected.

## The Solution

This skill gives your agent a structured memory system, a documentation verification habit, and a way of working that treats your project as an ongoing epic rather than a pile of disconnected tasks.

The workflow is optimized for clean restarts: each session leaves behind a compact reload packet, token/context-budget data, and an archived handoff so the next session can pick up with a fresh window while still using Entire for targeted transcript archaeology when full reasoning matters.

It orchestrates four systems:
- **[Superpowers](https://github.com/obra/superpowers)** — development rhythm (brainstorm → plan → execute → review)
- **[Entire](https://entire.io/)** — session recording, checkpoints, crash recovery
- **[Context7](https://github.com/upstash/context7)** — documentation verification with confidence scoring
- **[Codex](https://github.com/openai/codex)** — cross-model review, parallel debugging

## How It Works

**Before the first session**, the agent maps your entire codebase and writes `CODEBASE.md` — its long-term memory of your project.

**At the start of each feature**, the agent frames the work as an epic, cross-references against live library docs via Context7, and builds a verified plan.

**During each session**, the agent works through the plan, validates code against current documentation, and tracks every decision.

**At the end of each session**, the agent writes `HANDOFF.md` — a structured document capturing exactly where it stopped, what was verified, what's next, a compact reload packet, and the specific Entire checkpoints that recover deeper context.

**At the start of the next session**, the agent reads `HANDOFF.md` first, follows its compact reload packet, and then inspects the referenced Entire checkpoints as needed to recover the full reasoning path without blindly replaying everything.

## Quick Start

### 1. Install the skill

```bash
git clone https://github.com/neosekaleli/neo-skills.git
```

Copy into your project or Claude Code setup:

```bash
# Project-scoped
mkdir -p .claude/skills
cp -r neo-skills/skills/context-handoff .claude/skills/context-handoff

# Or global
mkdir -p ~/.claude/skills
cp -r neo-skills/skills/context-handoff ~/.claude/skills/context-handoff
```

### 2. Set up Context7

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

Optional: get a free API key at context7.com/dashboard for higher rate limits.

### 3. Enable Entire for Claude Code

```bash
npm install -g @anthropic/entire-cli
entire enable --agent claude-code
entire status
```

### 4. Set your Obsidian vault path (optional mirror)

```bash
export OBSIDIAN_VAULT=~/path/to/your/vault
```

If `OBSIDIAN_VAULT` is unset, the workflow still works. The repo remains the canonical store for `HANDOFF.md`, and repo-local memory in `.agent-memory/` keeps the handoff archive plus cumulative logs.

### 5. Start your first session

> "Read the context-handoff skill. This is a new project — start with Phase 0 (Codebase Understanding)."

### 6. Resume any future session

> "Load `HANDOFF.md`, run `entire status`, follow its Compact Reload Packet, inspect the referenced Entire checkpoints, then continue from the exact next action."

The next session should read `HANDOFF.md` first, run `entire status`, then the files listed in the reload packet, then the referenced Entire checkpoints or `entire explain --short` / `--full` / `--raw-transcript` targets as needed. That keeps the context window fresh while still making full transcript archaeology part of the normal resume flow.

## The Document Stack

| Document | Lifespan | Role |
|---|---|---|
| `CODEBASE.md` | Long-lived | Architecture, conventions, dependency map |
| `EPIC.md` | Per-epic | Business intent + technical approach |
| `PLAN.md` | Per-epic | Tasks + verified API contracts |
| `HANDOFF.md` | Per-session | Compact reload packet + resume index into Entire history |
| `decisions-log.md` | Cumulative | Institutional memory |

## Scope Note

This repo ships a Claude Code skill and Claude-oriented setup instructions. The underlying ideas are portable, but the install paths, slash commands, and integrations in this repo are written for Claude Code first.

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
