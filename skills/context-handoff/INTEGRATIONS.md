# Integrations: Context7, Codex, Entire

Referenced by `SKILL.md`. Detailed configuration and usage for each external system.

---

## Context7

### Setup
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
Or: `claude mcp add context7 -- npx -y @upstash/context7-mcp@latest`

Optional: free API key at `context7.com/dashboard` for higher rate limits.

### Two tools
- `resolve-library-id(libraryName, query)` → Context7 ID (cache per session)
- `query-docs(libraryId, query)` → current documentation + examples

### When to doc-check (mapped to pipeline)
| Pipeline Point | What to Check |
|---|---|
| During brainstorming | Library capabilities the design assumes |
| During writing-plans | Every library method in the plan |
| During TDD RED phase | Expected API behavior before writing test |
| During systematic-debugging | API behavior if hypothesis involves library |
| During integration checkpoints | Cross-system API contracts |
| On resume (gap >24h) | Re-verify previous LOW/NONE confidence checks |

### Confidence scoring
| Level | Meaning | Next Session |
|---|---|---|
| **HIGH** | Exact example for installed version | Trust |
| **MEDIUM** | Partial match or different version | Re-check if wrong |
| **LOW** | No docs for this version, extrapolating | Re-check before relying |
| **NONE** | No results returned | Manual check needed |

### Rules
- Cache `resolve-library-id` results (max 3 calls per library per session)
- Be specific: "Prisma findMany with nested include and pagination" not "Prisma query"
- Include installed version: "Next.js 14.2 middleware" not just "middleware"
- Log every check in handoff Doc Verifications table

---

## Codex

### Setup
```
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```
Requires ChatGPT subscription (incl. Free) or OpenAI API key + Node.js 18.18+.

### Four integration points

**1. After code review → adversarial review (high-risk only)**
```
/codex:adversarial-review --background challenge the auth implementation
```
When: auth, payments, concurrency, caching, public API, data migrations.
Skip: routine CRUD with good test coverage.

**2. During debugging → parallel investigation**
```
/codex:rescue --background investigate why integration tests fail
```
Two models approaching the same bug from different angles.

**3. When stuck → delegation**
```
/codex:rescue --background fix the flaky test with smallest safe patch
```
Keeps Claude's context focused on the main plan.

**4. Before finishing → final review**
```
/codex:review --base main --background
```
Full branch diff from a different model before merge.

### Background job management
- `/codex:status` — check running/completed jobs
- `/codex:result` — retrieve completed output
- `/codex:cancel` — cancel active job
- Jobs persist across sessions — record IDs in handoff

### Finding pattern tracking
Track in decisions-log.md. Same finding in 2+ sessions = systemic → fix root cause, not individual instances.

### Review gate warning
`/codex:setup --enable-review-gate` creates a Claude/Codex feedback loop. This workflow does NOT enable it by default. Use manual adversarial review at specific pipeline points instead.

---

## Entire

### Setup
```bash
npm install -g @anthropic/entire-cli
entire enable --agent claude-code
```

Use `entire configure` for follow-up changes such as enabling more agents, changing hook options, or adjusting settings.

### What it captures automatically
- Full conversation transcript (prompts + responses)
- All code changes
- Temporary checkpoints on local shadow branches during active sessions
- Permanent checkpoint metadata on commit, stored on `entire/checkpoints/v1`
- Token usage: input, output, cache creation, cache reads, and API call counts
- Line attribution (`Entire-Attribution: 73% agent (146/200 lines)`)
- Nested sessions from subagents

### Checkpoint mechanics
Every commit adds a trailer:
```
Add user authentication

Entire-Checkpoint: a3b2c4d5e6f7
Entire-Attribution: 73% agent (146/200 lines)
```

Checkpoints stored permanently on `entire/checkpoints/v1` branch (pushed to remote).

### Shadow branches
Format: `entire/<sessionID>-<worktreeID>`
- Local-only, auto-cleaned
- Enable mid-session rewind (even between commits)
- Crash recovery safety net

### Resume toolkit

Use these commands as the normal archaeology toolbelt:

```bash
# Check current Entire health and configuration
entire status
entire status --detailed

# Resume a branch's captured session state
entire resume <branch>

# Recover compact reasoning first
entire explain --commit <sha> --short

# Recover deeper transcript context when needed
entire explain --checkpoint <id> --full
entire explain --checkpoint <id> --raw-transcript

# Scope archaeology when multiple sessions/checkpoints are involved
entire explain --session <session-id> --short
entire explain --commit <sha> --search-all

# Repair or condense stuck session data
entire doctor
```

Preferred archaeology order:
- `entire status` before trusting local state
- `entire explain --short` for compact recall
- `entire explain --full` or `--raw-transcript` only when the handoff says deeper reasoning is required
- `entire doctor` if the session state or shadow branch looks stale or broken
- `entire rewind` only for active-session recovery when you intentionally want to move backward inside the current session

### Configuration tips

Useful Entire settings for this workflow:
- `.entire/settings.json` → `"strategy": "manual-commit"` for clean history and logical checkpoint boundaries
- `entire enable --agent claude-code --checkpoint-remote <provider:owner/repo>` when checkpoint metadata should live outside the main code repo
- `entire enable --agent claude-code --skip-push-sessions` if you intentionally do not want session logs pushed on `git push`
- `.entire/settings.json` → `strategy_options.push_sessions` for automatic checkpoint branch pushes
- `.entire/settings.json` → `strategy_options.summarize.enabled` for auto-generated summaries at commit time
- `entire enable --absolute-git-hook-path` if your team uses GUI Git clients

Notes:
- `manual-commit` is the recommended default for this workflow
- auto-summaries require Claude CLI to be installed and authenticated

Check effective settings with:

```bash
entire status --detailed
```

### How this skill uses Entire
- **HANDOFF.md references checkpoint IDs**, not transcript content
- Every handoff timing ✅ point should end at a logical commit so Entire has a durable checkpoint
- Recovery starts with `entire status`, then `entire explain --short`, then `--full`/`--raw-transcript` when needed
- `entire resume <branch>` is the preferred branch/session recovery path when restarting work on an existing branch
- `entire doctor` is the repair path for stuck or uncondensed sessions
- `entire rewind` is the active-session rewind tool, not the primary resume mechanism
- The Entire Checkpoints table in HANDOFF.md is the breadcrumb trail
- Entire token usage should be summarized into HANDOFF.md `Context Budget` and `Session Cost`, including input/output/cache/API breakdowns when available
- Resume should begin with `HANDOFF.md`, then follow its referenced Entire archaeology targets for the full reasoning path
- Rollback protocol reverts to a known-good checkpoint

### Key principle
HANDOFF.md stays compact BECAUSE Entire holds the full record. Don't duplicate — reference. The handoff is the index; Entire is the archive. Use repo-local handoff archives for guaranteed continuity, then mirror to Obsidian if configured.
