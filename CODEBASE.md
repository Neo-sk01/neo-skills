# Codebase: neo-skills
Last updated: 2026-04-12 - Session 1

## Architecture
`neo-skills` is a docs-first repository that ships a single Claude Code skill: `skills/context-handoff/`. There is no runtime application, API server, or library package here. The main "behavior" of the repo lives in Markdown instructions, templates, and integration guidance that teach an agent how to preserve continuity across sessions.

The core artifact is the `context-handoff` skill folder. `SKILL.md` defines the workflow, `REFERENCE.md` holds the heavier operational protocols, `HANDOFF-TEMPLATE.md` and `CODEBASE-TEMPLATE.md` define the durable documents that downstream projects should generate, and `INTEGRATIONS.md` explains how Superpowers, Entire, Context7, Codex, and the optional Obsidian mirror fit together. Root-level docs (`README.md`, `AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`) explain installation, usage, and editing conventions for humans and agents.

Because this is a workflow repo rather than a software service, correctness means keeping the instruction stack internally consistent. Changes usually span multiple docs: if the workflow changes in `SKILL.md`, supporting protocols, templates, and onboarding docs generally need matching updates. Entire is relevant here as repository-level session capture and resume infrastructure, not as an application dependency.

## File Structure
```text
.
├── .agent-memory/              # Repo-local memory for handoff archives and cumulative logs
├── .entire/                    # Local Entire project settings for this repo
├── docs/
│   ├── getting-started.md      # Installation and setup flow
│   └── onboarding-guide.md     # End-to-end workflow walkthrough
├── skills/
│   └── context-handoff/
│       ├── SKILL.md            # Main workflow contract
│       ├── REFERENCE.md        # Rollback, mutation, checkpoint, memory protocols
│       ├── INTEGRATIONS.md     # Entire / Context7 / Codex / Obsidian guidance
│       ├── HANDOFF-TEMPLATE.md # Session transfer template
│       └── CODEBASE-TEMPLATE.md# Phase 0 codebase map template
├── AGENTS.md                   # Agent-facing repo instructions
├── CLAUDE.md                   # Claude-oriented quick reference
├── CONTRIBUTING.md             # Change rules for the skill repo
├── HANDOFF.md                  # Latest session transfer for this repo
└── README.md                   # Public repo overview and install guide
```

## Conventions
- **Naming:** Root operating docs use uppercase names (`README.md`, `AGENTS.md`, `CLAUDE.md`, `HANDOFF.md`, `CODEBASE.md`). Skill support files use uppercase descriptive names inside `skills/context-handoff/`. General docs use kebab-case.
- **File organization:** Keep `SKILL.md` focused on actions and lifecycle flow. Put detailed operational rules in `REFERENCE.md`. Put reusable document shapes in templates instead of re-describing them inline.
- **Change scope:** If workflow behavior changes, update the corresponding README/onboarding/agent docs in the same pass so repo guidance does not drift.
- **Error handling:** This repo should state uncertainty explicitly. If a dependency or integration was not verified live, record that gap instead of implying it was checked.
- **Logging:** There is no runtime log stream. Verification evidence lives in handoffs, repo-local memory, and commit history.
- **Documentation style:** Use imperative, agent-facing language for workflow steps. Be concrete and operational rather than aspirational.
- **Verification style:** "Seems right" is not enough. Prefer command evidence (`entire status`, `git diff --check`, repo searches) and record what was actually observed.

## Test Patterns
- **Unit tests:** None configured.
- **Integration tests:** Manual workflow validation. The meaningful integration path is "resume -> inspect docs -> work -> handoff", not an application runtime.
- **E2E tests:** None configured.
- **Mocking strategy:** None; validation is primarily document consistency and CLI preflight checks.
- **Test data:** Embedded examples in templates and docs act as reference fixtures.

## Build & Deploy
- **Build command:** `n/a` (docs-only repository)
- **Test command:** `n/a` for automated tests; use `git diff --check` plus targeted workflow walkthroughs
- **Lint command:** `n/a`
- **CI/CD:** None present in the repository as of 2026-04-12
- **Environments:** Local repo editing; downstream usage comes from copying `skills/context-handoff/` into project-scoped or global Claude Code skill directories

## Cross-System Dependency Map
| System | Type | Interface | Contract | Health Check |
|---|---|---|---|---|
| Superpowers | Workflow/plugin layer | Referenced skills and slash-command flow | Provides brainstorming, planning, execution, review, and finishing rhythm expected by `SKILL.md` | Confirm required skills are available in the host session |
| Entire | CLI + git metadata layer | `entire enable`, `status`, `status --detailed`, `explain`, checkpoints branch | Captures session state, token telemetry, and resume archaeology without polluting the main branch | `entire status` |
| Context7 | MCP documentation layer | Live library lookups during planning and implementation | Verifies API/library docs before the workflow treats them as current | Confirm Context7 MCP is configured and can answer queries |
| Codex | Cross-model review layer | `/codex:*` commands or separate Codex session | Supplies adversarial review, rescue/debug help, and parallel investigation when needed | Confirm Codex plugin/tooling is installed in the host environment |
| Obsidian | Optional mirror | `OBSIDIAN_VAULT` file mirror path | Mirrors `HANDOFF.md` and repo-local memory; never replaces the repo as canonical store | Check `OBSIDIAN_VAULT` and destination path |

Dependencies modified this session: Entire local enablement for the `neo-skills` repo.

## Dependency Status (Context7 Audit)
| Library / Tool | Installed | Current | Breaking Changes? | Confidence |
|---|---|---|---|---|
| Entire CLI | Available locally and enabled for this repo | Not audited via live docs in this session | Unknown | LOW |
| Context7 MCP | Not configured in this environment | Not audited | Unknown | NONE |
| Codex integration | Documented in repo, not exercised in this session | Not audited | Unknown | NONE |
| Claude Code skill install flow | Documented in repo docs | Not audited against live external docs in this session | Unknown | LOW |

Phase 0 dependency audit is incomplete until Context7 is available in-session.

## Domain Knowledge
- This repository is the source of truth for a single continuity workflow, not a general skill pack anymore. Most edits should preserve the repo's "one skill, many supporting docs" shape.
- The key design distinction is that `HANDOFF.md` is a compact routing/index layer, while Entire is the deep archive for transcript archaeology. The workflow should avoid duplicating transcript detail into the handoff.
- Repo-local memory (`.agent-memory/`) is part of the workflow contract even when Obsidian is absent. Obsidian is a mirror, not the system of record.
- Entire setup matters at the repository level too. If this repo itself is used for testing the skill, `entire status` should be part of the local preflight.

## Known Tech Debt
| Area | Issue | Why It Exists | Risk |
|---|---|---|---|
| Verification | No automated doc or workflow test suite | Repo is currently maintained through manual reviews and command checks | Medium - drift can slip through if supporting docs are not updated together |
| External integrations | Context7, Codex, and Obsidian availability depend on host setup | The repo documents integrations but cannot enforce them from Markdown alone | Medium - users may think the stack is active when only parts are configured |
| Entire history | No checkpoint exists yet after local Entire enablement | Entire was enabled during bootstrap, after the latest commit | Low - future commits will fix this, but archaeology is limited today |

## Environment Variables
| Variable | Purpose | Required | Where Set |
|---|---|---|---|
| `OBSIDIAN_VAULT` | Optional mirror destination for handoffs and repo-local memory | No | User shell profile / environment |
