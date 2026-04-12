# Getting Started with neo-skills

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and configured

## Installation

### Claude Code (recommended)

Clone the repository and add it to your Claude Code configuration:

```bash
git clone https://github.com/neosekaleli/neo-skills.git
```

Then reference it in your project's CLAUDE.md or add the skills to your Claude Code setup.

### Manual installation

Copy the skills you want into your project:

```bash
# Copy a single skill
cp -r neo-skills/skills/context-handoff ~/.claude/skills/

# Or copy all skills
cp -r neo-skills/skills/* ~/.claude/skills/
```

## Integration Stack Setup

neo-skills works best with the full integration stack, but each integration is optional.

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

## Slash Commands

Once installed, use these commands in Claude Code:

| Command | What it does |
|---------|-------------|
| `/spec` | Refine an idea and produce a specification |
| `/plan` | Break a spec into implementation tasks |
| `/build` | Implement tasks incrementally |
| `/test` | Write tests using TDD (RED-GREEN-REFACTOR) |
| `/review` | Five-axis code review with optional Codex |
| `/debug` | Systematic debugging with rollback protocol |
| `/ship` | Pre-launch checks, deploy, and archive session |

## First Run

1. Start Claude Code in your project directory
2. Run `/spec` with your feature idea
3. Follow the skill workflow — it will guide you through each step
4. The skills chain naturally: spec -> plan -> build -> test -> review -> ship
