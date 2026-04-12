# Getting Started

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and configured

## Installation

### Clone the repo

```bash
git clone https://github.com/neosekaleli/neo-skills.git
```

### Install the skill

Copy into your project or Claude Code setup:

```bash
# Project-scoped
mkdir -p .claude/skills
cp -r neo-skills/skills/context-handoff .claude/skills/context-handoff

# Or global
mkdir -p ~/.claude/skills
cp -r neo-skills/skills/context-handoff ~/.claude/skills/context-handoff
```

## Integration Stack Setup

The skill works best with the full integration stack, but Obsidian is optional and handled as a mirror. The repo and `.agent-memory/` stay sufficient for handoffs and cumulative logs.

### Context7 (documentation verification)

Add to your Claude Code MCP config:
```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

Or in `settings.json`:
```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    }
  }
}
```

Optional: get a free API key at context7.com/dashboard for higher rate limits.

### Codex (cross-model review)

```
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```

Requires ChatGPT subscription (incl. Free) or OpenAI API key + Node.js 18.18+.

### Entire (session recording)

```bash
npm install -g @anthropic/entire-cli
entire enable --agent claude-code
```

Useful follow-up commands:

```bash
entire status
entire status --detailed
```

### Superpowers

Superpowers is a Claude Code plugin that provides the development rhythm (brainstorming, planning, execution, review). See the Superpowers documentation for setup.

### Obsidian (optional mirror)

Add to your shell profile:
```bash
export OBSIDIAN_VAULT=~/path/to/your/vault
```

If you don't use Obsidian, the workflow still functions. `HANDOFF.md` stays in the repo, and `.agent-memory/` stores the handoff archive plus repo-local copies of `FAILURES.md` and `decisions-log.md`.

## First Run

1. Start Claude Code in your project directory
2. Tell your agent:
   > "Read the context-handoff skill. This is a new project — start with Phase 0 (Codebase Understanding)."
3. The agent will explore your repo, map the architecture, audit dependencies against live docs, and produce `CODEBASE.md`

## Resuming a Session

Point the new session at the latest `HANDOFF.md` and say:

> "Load `HANDOFF.md`, use its Compact Reload Packet to decide what to read next, inspect the referenced Entire checkpoints, then continue."

The agent should read `HANDOFF.md` first, run `entire status`, then load the required files listed in the reload packet, then use the referenced `entire explain --short` / `--full` / `--raw-transcript` targets as needed. That keeps the next session's context window fresh while still recovering the full reasoning path when the handoff points to it.

## Next Steps

Read the [onboarding guide](onboarding-guide.md) for a detailed walkthrough of the full workflow.
