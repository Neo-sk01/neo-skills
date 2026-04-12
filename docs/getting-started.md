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
cp -r neo-skills/skills/context-handoff .claude/skills/context-handoff

# Or global
cp -r neo-skills/skills/context-handoff ~/.claude/skills/context-handoff
```

## Integration Stack Setup

The skill works best with the full integration stack, but each integration is optional.

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
entire init   # run in your repo
```

### Superpowers

Superpowers is a Claude Code plugin that provides the development rhythm (brainstorming, planning, execution, review). See the Superpowers documentation for setup.

### Obsidian (optional)

Add to your shell profile:
```bash
export OBSIDIAN_VAULT=~/path/to/your/vault
```

If you don't use Obsidian, the workflow still functions — the Git-side persistence (HANDOFF.md in your repo) works independently. You just won't get the archiving, decisions log, or long-term knowledge base features.

## First Run

1. Start Claude Code in your project directory
2. Tell your agent:
   > "Read the context-handoff skill. This is a new project — start with Phase 0 (Codebase Understanding)."
3. The agent will explore your repo, map the architecture, audit dependencies against live docs, and produce `CODEBASE.md`

## Resuming a Session

Drop the latest `HANDOFF.md` into a new session and say:

> "Read HANDOFF.md and continue."

The agent will load context, run preflight checks, and pick up where the last session left off.

## Next Steps

Read the [onboarding guide](onboarding-guide.md) for a detailed walkthrough of the full workflow.
