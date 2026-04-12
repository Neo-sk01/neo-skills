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
entire init   # run in repo
```

### What it captures automatically
- Full conversation transcript (prompts + responses)
- All code changes
- Checkpoints on every git commit
- Token usage (input/output, cache, API calls)
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

### How this skill uses Entire
- **HANDOFF.md references checkpoint IDs**, not transcript content
- Every handoff timing ✅ point coincides with a commit = checkpoint
- Recovery: `entire explain <commit>` reconstructs reasoning
- The Entire Checkpoints table in HANDOFF.md is the breadcrumb trail
- Rollback protocol reverts to a known-good checkpoint

### Key principle
HANDOFF.md is concise BECAUSE Entire holds the full record. Don't duplicate — reference.
