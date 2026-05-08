# The Agentic Context Handoff Workflow
## A Developer's Onboarding Guide

---

## What Is This?

You've just installed a skill that turns your AI coding agent from a forgetful assistant into a persistent engineering partner. 

Here's the problem it solves: every time you start a new chat session with an AI coding agent, it starts from zero. In Claude Code especially, that means a fresh context window with none of yesterday's reasoning loaded. It doesn't know what you built yesterday, what decisions you made, why you chose Prisma over Drizzle, or that the auth middleware has a quirk on the `/api/webhooks` route. You end up repeating yourself, re-explaining context, and watching the agent make the same mistakes you already corrected in a previous session.

This workflow eliminates that. It gives your agent a structured memory system, a documentation verification habit, and a way of working that treats your project as an ongoing epic rather than a pile of disconnected tasks.

---

## The Core Idea in 60 Seconds

Your agent works in sessions. Each session has a beginning, middle, and end. This workflow formalises that lifecycle:

**Before the first session**, the agent maps your entire codebase — architecture, conventions, dependencies, domain logic — and writes it down in a living document called `CODEBASE.md`. This is its long-term memory of your project.

**At the start of each feature**, the agent frames the work as an epic with business intent and technical approach, then builds a phased plan. No user stories, no sprint boards — just a clear sequence of what to build and in what order.

**During each session**, the agent works through the plan, validates its code against live library documentation using Context7, runs tests continuously, tracks every decision it makes, and checks context usage at natural boundaries.

**At the end of each session**, before context runs out, the agent writes a structured handoff document — `HANDOFF.md` — that captures exactly where it stopped, what it verified, what's next, and what the next session needs to know. If context used reaches 150,000 tokens, the agent starts the handoff automatically at the next safe atomic stop instead of beginning more work. The handoff starts with a compact reload packet so the next session can resume with a clean context window, and it points to the exact Entire checkpoints that recover the deeper reasoning. The latest handoff is saved in your Git repo, the previous one is archived in repo-local memory, and everything can be mirrored to Obsidian if you use it.

**At the start of the next session**, you point the agent at `HANDOFF.md` and tell it to follow the reload packet. The agent reads that first, runs a preflight check to make sure the repo matches what the handoff claims, loads the additional files it actually needs, and then uses the referenced Entire checkpoints to recover the full reasoning path where needed.

No context lost. No re-explaining. No starting over.

---

## What Problems Does This Actually Solve?

### 1. The Amnesia Problem

AI agents have no memory between sessions. Without this workflow, every new chat is a blank slate. You waste the first 10–15 minutes of every session re-establishing context — explaining your architecture, reminding the agent about decisions, re-describing the task. Multiply that by 5 sessions a day and you've lost over an hour to context re-establishment alone.

**How this workflow fixes it:** The `HANDOFF.md` carries forward everything the agent needs. The `CODEBASE.md` provides persistent architectural knowledge. The `decisions-log.md` in Obsidian accumulates your project's institutional memory over weeks and months. The agent doesn't just remember what it was doing — it remembers *why*.

### 2. The Hallucination Problem

AI agents confidently use APIs that don't exist, function signatures that changed two versions ago, and patterns that were deprecated last month. This is the single largest source of wasted time in AI-assisted development. You write code, it looks right, it compiles — then fails at runtime because the agent hallucinated an API.

**How this workflow fixes it:** Context7 integration is baked into every phase. Before the agent writes code that touches a library, it queries Context7 for current, version-specific documentation. It checks that the function exists, that the parameters are correct, that the pattern matches the installed version. Every verification gets logged in the handoff so the next session doesn't repeat the lookup but also knows what was confirmed and when.

### 3. The "Works But Doesn't Fit" Problem

The agent writes code that passes its own tests but clashes with your codebase — different naming conventions, different error handling patterns, different file organisation, different test structure. The code review friction alone can take longer than writing the code manually.

**How this workflow fixes it:** Phase 0 (Codebase Understanding) maps your conventions explicitly. The agent knows you use camelCase, that your tests live in `__tests__/` directories, that you handle errors with a custom `AppError` class, that your API responses follow a specific envelope format. It follows your patterns, not generic ones.

### 4. The "Lost in the Middle" Problem

Long coding sessions degrade in quality. The agent's responses get less precise, it forgets earlier context, it starts contradicting its own earlier decisions. You don't notice until you review the output and find inconsistencies.

**How this workflow fixes it:** The handoff trigger system detects context pressure before it becomes a problem. The hard trigger is 150,000 tokens used: once the agent reaches that point, it finishes the smallest safe atomic unit, generates a comprehensive handoff, and lets the next session start fresh with full context. A clean handoff at 150k beats a sloppy continuation near the edge of the window.

### 5. The Task-Level Thinking Problem

Most AI coding workflows operate at the task level: "write this function," "fix this bug," "add this endpoint." The agent has no sense of the bigger picture — how this function fits into the feature, how the feature fits into the system, what tradeoffs were made and why. This produces locally optimal but globally incoherent code.

**How this workflow fixes it:** The workflow operates at the epic level. The agent holds the full business intent (EPIC.md), the full technical plan (PLAN.md), and the full architectural context (CODEBASE.md). When it writes a function, it knows where that function sits in the larger feature, what comes before and after it in the plan, and what business goal it serves. Decisions are made with full context, not local guesses.

### 6. The Crash Recovery Problem

Your session dies — network drop, browser crash, context limit hit mid-sentence. Everything from that session is gone. You have the Git commits, but you've lost the reasoning, the decisions, the "I was about to try X next" context.

**How this workflow fixes it:** The repo keeps the current `HANDOFF.md`, `.agent-memory/handoff-archive/` keeps previous handoffs, and Obsidian can mirror both if configured. Even if a session crashes before generating a proper handoff, the previous session's handoff is still intact in the archive. You never lose more than one session's worth of context, and the Git history (via Entire) provides the commit-level black box recording.

---

## The Workflow, Step by Step

### Phase 0 — Codebase Understanding

**When:** First session on a project, or after major structural changes.

**What happens:** The agent explores your entire repository — not just the files relevant to the current task, but the full architecture. It reads your package manifests, traces your dependency graph, identifies your conventions, and maps your domain logic. Then it writes `CODEBASE.md`, a living reference document that every future session will load.

**The Context7 checkpoint:** After mapping dependencies, the agent runs a doc-check on every major library. It compares your installed versions against current docs, flags breaking changes, and notes any deprecated patterns your codebase is currently using. This gets recorded in `CODEBASE.md` under "Dependency Status."

**What you get:** A comprehensive, written-down understanding of your system that the agent can reload in seconds instead of re-deriving over minutes. Think of it as the onboarding document you'd write for a new team member — except the agent writes it for itself and keeps it updated.

### Phase 1 — Epic Framing

**When:** Starting a new feature or project.

**What happens:** You describe what you want to build. The agent cross-references your requirements against `CODEBASE.md` to assess technical feasibility — what can be reused, what needs to change, where the risk is. It produces `EPIC.md` with the business intent, technical approach, scope boundaries, and a risk register.

**The Context7 checkpoint:** Before finalising the approach, the agent queries live docs for every library capability it's planning to use. "Can Prisma do composite keys?" "Does the Stripe API support idempotent retries?" If the docs contradict the plan, the plan changes before any code is written.

**What you get:** A plan that's grounded in both your codebase reality and current library capabilities. No more building on assumptions that turn out to be wrong three days into implementation.

### Phase 2 — Plan Design

**When:** After EPIC.md is approved.

**What happens:** The agent generates a detailed technical action plan — every file to touch, the implementation order, the validation strategy, and the natural handoff boundaries where work can be cleanly paused between sessions.

**The Context7 checkpoint:** Every library method referenced in the plan gets verified. The agent confirms function signatures, parameter types, and return values against live docs. Results go into a "Verified APIs" table in `PLAN.md`.

**What you get:** An implementation roadmap where every API contract has been pre-verified. When the agent starts coding, it's executing a validated plan, not improvising.

### Phase 3 — Session Lifecycle

This is the repeating loop that runs for every coding session.

**Resume:** Load `HANDOFF.md` first. Use its compact reload packet to decide which supporting docs to load next and in what order, then inspect the referenced Entire checkpoints or `entire explain` outputs for deeper reasoning. Run a preflight check — does the repo's HEAD match the handoff's last commit? Are there unexpected dirty files? Do the tests still pass? Is the recorded context budget still realistic? If anything is off, the agent flags it and asks before proceeding.

**Work:** Follow the plan phase by phase. Use Superpowers cycles (brainstorm → plan → TDD → review) for each unit of work. Run tests after each meaningful change, not just at the end. Track decisions, discoveries, and blockers as they happen.

**Doc-Check During Work:** Every time the agent is about to use a library API it hasn't verified this session, it queries Context7 first. This isn't optional — it's a mandatory checkpoint. If the API doesn't match expectations, the agent adjusts before writing broken code.

**Handoff:** When a plan phase completes, a blocker is hit, or context pressure builds, the agent finishes its current atomic unit and generates `HANDOFF.md`. Context pressure has a hard trigger: at 150,000 tokens used, the agent starts handoff at the next safe atomic stop and records whether the measurement was exact or estimated. This captures a compact reload packet, the Entire archaeology targets for full-context recovery, commit SHA, dirty files, completed work, current task, next actions, decisions made, doc verifications performed, codebase discoveries, environment snapshot, and token/context-budget tracking. It archives the previous handoff in `.agent-memory/`, writes the new handoff to the repo root, and mirrors to Obsidian if configured.

---

## The Document Stack

Your project accumulates six documents, each with a specific role:

**`CODEBASE.md`** is the long-term architectural reference. It describes your system's structure, conventions, and dependency status. It lives in your repo and can be mirrored to Obsidian. It's updated whenever the agent discovers something new about your codebase. Lifespan: months. Updated incrementally.

**`EPIC.md`** captures the business intent and technical approach for a feature. It's the "why" and the "how at a high level." One per epic. It ensures that technical decisions stay connected to business goals across sessions.

**`PLAN.md`** is the ordered implementation roadmap with verified API contracts. It's the "what to do, in what order, and what was already confirmed." One per epic. It lives in the repo so the agent can track progress.

**`HANDOFF.md`** is the session state transfer document. It starts with a compact reload packet so a brand-new session can resume quickly, and it points to the specific Entire checkpoints needed for deeper reasoning. The current copy lives at repo root, while previous copies are archived in `.agent-memory/handoff-archive/` and optionally mirrored to Obsidian.

**`decisions-log.md`** is the cumulative architectural knowledge base in repo-local memory and can be mirrored to Obsidian. Every non-trivial decision from every session gets appended here with rationale and reversibility. Over time, this becomes your project's institutional memory — why things are the way they are.

**Handoff archive** is the timestamped history of every session's handoff in `.agent-memory/handoff-archive/` and optionally Obsidian. It's your crash recovery safety net and your audit trail of how the project evolved session by session.

---

## How Context7 Makes This Better Than "Regular" AI Coding

Most AI coding workflows trust the agent's training data. This workflow does not. Training data is a starting point, but it's never the authority. The authority is the live documentation, and Context7 is how the agent accesses it.

Here's the practical difference:

**Without Context7:** The agent writes `const router = useRouter()` in a Next.js 15 App Router component. This was correct in Next.js 13 Pages Router. In App Router, it's `import { useRouter } from 'next/navigation'`, not `'next/router'`. The code compiles. The tests might even pass in isolation. But it breaks at runtime. You spend 20 minutes debugging an import path.

**With Context7:** Before writing that line, the agent calls `query-docs("/vercel/next.js", "useRouter App Router")` and gets the current documentation showing the correct import. The correct code is written the first time.

This happens at five checkpoints across the workflow, not just during coding. By the time the agent starts writing code, it has already verified the feasibility of the approach (Phase 1), the API contracts in the plan (Phase 2), and the current state of libraries since the last session (Phase 3a). The actual implementation verification (Phase 3b) is the final safety net, not the only one.

Every verification is logged in the handoff's "Doc Verifications" table. The next session can see what was checked, when, and what the findings were. If a library released a patch between sessions, the resumption verification (Phase 3a) catches it by re-checking the previous session's verifications.

---

## What This Means for You as a Developer

### Your role shifts, not shrinks

You stop being the person who re-explains context and catches hallucinated APIs. You start being the person who defines what to build, reviews the plan, approves the approach, and makes the judgment calls the agent can't.

The workflow handles: context management, documentation verification, session continuity, decision tracking, convention enforcement.

You handle: business requirements, architectural judgment, plan review, code review, deployment decisions.

### Your knowledge compounds

Every session adds to `decisions-log.md` and `CODEBASE.md`. After a month of work, you have a comprehensive architectural knowledge base that any team member (human or AI) can reference. This is institutional memory that normally lives in people's heads and walks out the door when they leave.

### Your sessions are more productive

No more spending the first 15 minutes re-establishing context. No more debugging hallucinated APIs. No more discovering at code review that the agent used the wrong conventions. The preflight check catches drift, the doc-checks catch API issues, and the convention tracking catches style mismatches — all before they become your problem.

### You can walk away mid-project

Need to switch to a different project for a week? The handoff system doesn't care. Your context is in files, not in your head or in a chat window. Come back, point the next session at `HANDOFF.md`, tell it to follow the reload packet, and you're exactly where you left off. The agent even checks that nothing drifted while you were away.

### You can onboard others

Because the knowledge is in documents — not in chat histories alone — another developer (or another AI agent) can pick up your project by reading `CODEBASE.md`, `EPIC.md`, `PLAN.md`, and the latest `HANDOFF.md`. The decisions log tells them why things are the way they are. The handoff archive tells them how the project evolved. The compact reload packet tells them what to read first, and Entire tells them the full reasoning path behind it.

---

## Getting Started

### 1. Install the skill

Install the entire `context-handoff/` folder, not just `SKILL.md`, because the workflow also relies on `REFERENCE.md`, `INTEGRATIONS.md`, and the templates.

- **Project-scoped:** `.claude/skills/context-handoff/`
- **Global:** `~/.claude/skills/context-handoff/`

### 2. Set up Context7

Add to your MCP config:

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

Or via Claude Code CLI:

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

Optionally, get a free API key at `context7.com/dashboard` for higher rate limits.

### 3. Set your Obsidian vault path

Add to your shell profile (`.zshrc`, `.bashrc`, etc.):

```bash
export OBSIDIAN_VAULT=~/path/to/your/vault
```

If you don't use Obsidian, the workflow still functions. `HANDOFF.md` remains in the repo root, and `.agent-memory/` stores the handoff archive plus repo-local memory docs like `FAILURES.md` and `decisions-log.md`.

### 3a. Enable Entire for Claude Code

Run in your repository:

```bash
npm install -g @anthropic/entire-cli
entire enable --agent claude-code
```

Helpful follow-up commands:

```bash
entire status
entire status --detailed
```

### 4. Start your first session

Tell your agent:

> "Read the context-handoff skill. This is a new project — start with Phase 0 (Codebase Understanding)."

The agent will explore your repo, map the architecture, audit dependencies against live docs, and produce `CODEBASE.md`. From there, you're in the workflow.

### 5. Resume any future session

Point the new session at the latest `HANDOFF.md` and say:

> "Load `HANDOFF.md`, run `entire status`, follow its Compact Reload Packet, inspect the referenced Entire checkpoints, then continue."

The agent will load the handoff first, run `entire status`, use the reload packet to keep the new context window clean, inspect the referenced Entire checkpoints for deeper context, run preflight checks, and pick up where the last session left off.

---

## FAQ

**Does this work with any AI coding agent?**
The packaged skill and setup instructions are written for Claude Code. The principles are portable, and other agents can adopt the document format, but the install paths, slash commands, and integration guidance in this repo are Claude-first.

**Do I need Obsidian?**
No. Obsidian is an optional mirror. The required persistence layer is your repo plus `.agent-memory/`. Obsidian just gives you a nicer place to browse the same information.

**How much overhead does this add?**
Phase 0 adds 10–15 minutes to your first session on a project. After that, the resume preflight takes under a minute. The doc-checks add a few seconds per API verification. The handoff generation takes 1–2 minutes at the end of a session. Net effect: you save far more time on re-establishing context and debugging hallucinated APIs than you spend on the workflow overhead.

**What if the agent's context fills up before it can generate a handoff?**
The workflow's handoff triggers are designed to fire before this happens, and the handoff template explicitly records token/context-budget data so the next session starts fresh instead of bloated. If a session still dies early, the previous session's handoff is still in `.agent-memory/handoff-archive/` (and Obsidian if configured), and Git history plus Entire transcript archaeology provide the full reasoning record.

**What Entire commands matter most for resuming work?**
Start with `entire status`, then use `entire explain --commit <sha> --short` for compact recall. Escalate to `entire explain --checkpoint <id> --full` or `--raw-transcript` only when the handoff says the deeper reasoning matters. Use `entire resume <branch>` when restoring work on an existing branch and `entire doctor` if the local session state looks stuck.

**Can multiple agents work on the same project?**
Yes. Each agent reads `HANDOFF.md` from the repo, follows the reload packet, uses the referenced Entire checkpoints for deeper context, works, and writes a new one. The repo-local archive keeps the history. Concurrent work on different features would need separate branches with separate handoff files, just as concurrent human developers would use separate branches.

**What if I disagree with a decision the agent logged?**
Override it. The decisions log is a record, not a contract. Tell the agent in the next session: "Reverse the decision to use X, we're switching to Y because Z." It'll update the log with the reversal and adjust the plan accordingly.

---

## Quick Reference Card

| I want to... | Do this |
|---|---|
| Start a new project | "Start with Phase 0 — map this codebase" |
| Begin a new feature | "Frame an epic for {feature description}" |
| Continue work | Open `HANDOFF.md` → "Load the Compact Reload Packet, run `entire status`, inspect the referenced Entire checkpoints, then continue" |
| Force a handoff | "Wrap up and generate a handoff" |
| Check a library's docs | "Use Context7 to verify {library} {method}" |
| Review the decisions log | Open `.agent-memory/decisions-log.md` (or Obsidian mirror) |
| See session history | Browse `.agent-memory/handoff-archive/` (or Obsidian mirror) |
| Update codebase knowledge | "Update CODEBASE.md with what we learned this session" |
| Onboard a teammate | Share `CODEBASE.md`, latest `EPIC.md`, and `HANDOFF.md` |
