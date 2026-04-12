# neo-skills Repository Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a publishable open-source repository of 12 production-grade engineering skills for AI coding agents, integrated with Superpowers, Entire, Context7, and Codex.

**Architecture:** Osmani-style directory layout (skills/, agents/, references/, docs/, .claude/commands/) with Claude Code as the sole target platform. Existing context-handoff files move into skills/context-handoff/. All other skills are new. Each skill follows a standard anatomy with YAML frontmatter, integration points, rationalizations table, and verification checklist.

**Tech Stack:** Markdown, Claude Code slash commands, git

---

## File Map

| Action | Path | Responsibility |
|--------|------|---------------|
| Create | `.gitignore` | Repo hygiene |
| Create | `LICENSE` | MIT license |
| Create | `CLAUDE.md` | Project rules for Claude Code |
| Create | `AGENTS.md` | AI agent guidance |
| Move | `SKILL.md` → `skills/context-handoff/SKILL.md` | Existing skill |
| Move | `REFERENCE.md` → `skills/context-handoff/REFERENCE.md` | Existing skill |
| Move | `INTEGRATIONS.md` → `skills/context-handoff/INTEGRATIONS.md` | Existing skill |
| Move | `HANDOFF-TEMPLATE.md` → `skills/context-handoff/HANDOFF-TEMPLATE.md` | Existing skill |
| Move | `CODEBASE-TEMPLATE.md` → `skills/context-handoff/CODEBASE-TEMPLATE.md` | Existing skill |
| Create | `.claude/commands/spec.md` | Slash command |
| Create | `.claude/commands/plan.md` | Slash command |
| Create | `.claude/commands/build.md` | Slash command |
| Create | `.claude/commands/test.md` | Slash command |
| Create | `.claude/commands/review.md` | Slash command |
| Create | `.claude/commands/debug.md` | Slash command |
| Create | `.claude/commands/ship.md` | Slash command |
| Create | `agents/code-reviewer.md` | Agent persona |
| Create | `agents/test-engineer.md` | Agent persona |
| Create | `agents/security-auditor.md` | Agent persona |
| Create | `references/testing-patterns.md` | Reference checklist |
| Create | `references/security-checklist.md` | Reference checklist |
| Create | `references/performance-checklist.md` | Reference checklist |
| Create | `skills/idea-refine/SKILL.md` | Skill |
| Create | `skills/spec-driven-development/SKILL.md` | Skill |
| Create | `skills/planning-and-task-breakdown/SKILL.md` | Skill |
| Create | `skills/incremental-implementation/SKILL.md` | Skill |
| Create | `skills/test-driven-development/SKILL.md` | Skill |
| Create | `skills/context-engineering/SKILL.md` | Skill |
| Create | `skills/frontend-ui-engineering/SKILL.md` | Skill |
| Create | `skills/debugging-and-error-recovery/SKILL.md` | Skill |
| Create | `skills/code-review/SKILL.md` | Skill |
| Create | `skills/code-simplification/SKILL.md` | Skill |
| Create | `skills/shipping-and-launch/SKILL.md` | Skill |
| Create | `docs/getting-started.md` | Setup guide |
| Create | `docs/skill-anatomy.md` | Contributor guide |
| Create | `README.md` | Project overview |
| Create | `CONTRIBUTING.md` | Contribution guide |

---

### Task 1: Repository Foundation

**Files:**
- Create: `.gitignore`
- Create: `LICENSE`

- [ ] **Step 1: Create .gitignore**

```
# OS
.DS_Store
Thumbs.db

# Editor
.vscode/
.idea/
*.swp
*.swo
*~

# Node
node_modules/
npm-debug.log*

# Environment
.env
.env.local
.env.*.local

# Build
dist/
build/
*.zip

# Obsidian (local project docs)
.obsidian/
```

- [ ] **Step 2: Create MIT LICENSE**

Use the standard MIT license text with:
- Year: 2026
- Copyright holder: Neo Sekaleli

```
MIT License

Copyright (c) 2026 Neo Sekaleli

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

- [ ] **Step 3: Commit**

```bash
git add .gitignore LICENSE
git commit -m "chore: add .gitignore and MIT license"
```

---

### Task 2: CLAUDE.md

**Files:**
- Create: `CLAUDE.md`

- [ ] **Step 1: Write CLAUDE.md**

```markdown
# neo-skills

Production-grade engineering skills for AI coding agents, integrated with Superpowers, Entire, Context7, and Codex.

## Skill Rules

- Every skill lives in `skills/<name>/SKILL.md`
- Check for an applicable skill before starting any work — even a 1% chance means check
- Skills are workflows, not suggestions — follow steps in order, don't skip verification
- Multiple skills can chain: idea-refine → spec → plan → build → test → review → ship
- "Seems right" is never sufficient — verification requires evidence (test output, build results, runtime data)

## Core Operating Behaviors

1. **Surface assumptions** — before implementing anything non-trivial, state your assumptions explicitly and ask for correction
2. **Manage confusion actively** — when you hit inconsistencies or unclear specs, STOP. Name the confusion. Ask. Don't guess.
3. **Push back when warranted** — sycophancy is a failure mode. Point out problems, quantify downsides, propose alternatives. Accept override with full information.
4. **Enforce simplicity** — prefer the boring, obvious solution. If 100 lines suffice, 1000 lines is a failure. Ask: "Would a staff engineer say 'why didn't you just...?'"
5. **Maintain scope discipline** — touch only what you're asked to touch. Don't clean up adjacent code, remove comments you don't understand, or add unrequested features.
6. **Verify, don't assume** — every skill has a verification step. A task is not complete until verification passes.

## Integration Stack

- **Superpowers** — development rhythm: brainstorming → planning → execution → review → finishing
- **Entire** — session recording: checkpoints on every commit, full transcripts, crash recovery
- **Context7** — documentation verification: resolve library IDs, query current docs, confidence scoring (HIGH/MEDIUM/LOW/NONE)
- **Codex** — cross-model review: adversarial review on high-risk code, parallel debugging, background delegation

## Skill Discovery

```
Task arrives
    ├── Vague idea? ───────────────→ idea-refine
    ├── New feature/change? ───────→ spec-driven-development
    ├── Have spec, need tasks? ────→ planning-and-task-breakdown
    ├── Implementing? ─────────────→ incremental-implementation
    │   ├── UI work? ──────────────→ frontend-ui-engineering
    │   └── Need context? ─────────→ context-engineering
    ├── Writing tests? ────────────→ test-driven-development
    ├── Something broke? ──────────→ debugging-and-error-recovery
    ├── Reviewing code? ───────────→ code-review
    │   └── Too complex? ──────────→ code-simplification
    ├── Ready to deploy? ──────────→ shipping-and-launch
    └── Session ending? ───────────→ context-handoff
```

## Conventions

- Skill directories: `kebab-case`
- Skill files: `SKILL.md` (always uppercase)
- Supporting files: `UPPERCASE.md` (e.g., `REFERENCE.md`, `INTEGRATIONS.md`)
- YAML frontmatter required on every `SKILL.md`
- Skills stay under 500 lines — heavy reference goes in supporting files
```

- [ ] **Step 2: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: add CLAUDE.md with project rules and skill discovery"
```

---

### Task 3: AGENTS.md

**Files:**
- Create: `AGENTS.md`

- [ ] **Step 1: Write AGENTS.md**

```markdown
# AGENTS.md

This file provides guidance to AI coding agents working with code in this repository.

## Repository Overview

neo-skills is a collection of production-grade engineering skills for AI coding agents. Skills are structured workflows — not reference docs — that enforce verification at every step. The collection is opinionated toward a four-tool integration stack: Superpowers, Entire, Context7, and Codex.

## Core Rules

- If a task matches a skill, you MUST invoke it
- Skills are located in `skills/<skill-name>/SKILL.md`
- Never implement directly if a skill applies
- Follow skill instructions exactly — do not partially apply them
- Every skill has a verification step. "Seems right" is never sufficient.

## Intent to Skill Mapping

Map user intent to skills automatically:

- Vague idea / brainstorming → `idea-refine`
- Feature / new functionality → `spec-driven-development`, then `incremental-implementation`, `test-driven-development`
- Planning / breakdown → `planning-and-task-breakdown`
- Bug / failure / unexpected behavior → `debugging-and-error-recovery`
- Code review → `code-review`
- Refactoring / simplification → `code-simplification`
- UI work → `frontend-ui-engineering`
- Context management → `context-engineering`
- Deploying / launching → `shipping-and-launch`
- Session ending / handoff → `context-handoff`

## Lifecycle Sequence

For a complete feature:

```
1. idea-refine                 → Refine vague ideas
2. spec-driven-development     → Define what to build
3. planning-and-task-breakdown → Break into verifiable tasks
4. context-engineering         → Load the right context
5. incremental-implementation  → Build slice by slice
6. test-driven-development     → Prove each slice works
7. code-review                 → Review before merge
8. shipping-and-launch         → Deploy safely
9. context-handoff             → Archive session for continuity
```

Not every task needs every skill. A bug fix might only need: `debugging-and-error-recovery` → `test-driven-development` → `code-review`.

## Anti-Rationalization

These thoughts are incorrect:

| Thought | Reality |
|---------|---------|
| "This is too small for a skill" | Small tasks are where unexamined assumptions cause the most waste |
| "I can just quickly implement this" | Quick implementation without process is the top failure mode |
| "I'll gather context first" | Skills tell you HOW to gather context — check first |
| "I know how to do this" | Knowing the concept is not the same as following the workflow |

## Creating a New Skill

See `docs/skill-anatomy.md` for the required format. Every skill must include:
- YAML frontmatter with `name` and `description`
- Overview, When to Use, Integration Points, Core Process
- Common Rationalizations table
- Red Flags list
- Verification checklist
```

- [ ] **Step 2: Commit**

```bash
git add AGENTS.md
git commit -m "docs: add AGENTS.md with agent guidance and skill mapping"
```

---

### Task 4: Move Context-Handoff Skill

**Files:**
- Move: `SKILL.md` → `skills/context-handoff/SKILL.md`
- Move: `REFERENCE.md` → `skills/context-handoff/REFERENCE.md`
- Move: `INTEGRATIONS.md` → `skills/context-handoff/INTEGRATIONS.md`
- Move: `HANDOFF-TEMPLATE.md` → `skills/context-handoff/HANDOFF-TEMPLATE.md`
- Move: `CODEBASE-TEMPLATE.md` → `skills/context-handoff/CODEBASE-TEMPLATE.md`

- [ ] **Step 1: Create directory and move files**

```bash
mkdir -p skills/context-handoff
git mv SKILL.md skills/context-handoff/SKILL.md
git mv REFERENCE.md skills/context-handoff/REFERENCE.md
git mv INTEGRATIONS.md skills/context-handoff/INTEGRATIONS.md
git mv HANDOFF-TEMPLATE.md skills/context-handoff/HANDOFF-TEMPLATE.md
git mv CODEBASE-TEMPLATE.md skills/context-handoff/CODEBASE-TEMPLATE.md
```

- [ ] **Step 2: Update the SKILL.md internal references**

The existing SKILL.md references `examples/CODEBASE-TEMPLATE.md`. Update to `CODEBASE-TEMPLATE.md` (now co-located):

Change line 11 from:
```
> Codebase template → `examples/CODEBASE-TEMPLATE.md`
```
To:
```
> Codebase template → `CODEBASE-TEMPLATE.md`
```

- [ ] **Step 3: Verify files moved correctly**

```bash
ls -la skills/context-handoff/
```

Expected: 5 files — SKILL.md, REFERENCE.md, INTEGRATIONS.md, HANDOFF-TEMPLATE.md, CODEBASE-TEMPLATE.md

- [ ] **Step 4: Commit**

```bash
git add -A skills/context-handoff/
git commit -m "refactor: move context-handoff skill into skills/ directory"
```

---

### Task 5: Slash Commands

**Files:**
- Create: `.claude/commands/spec.md`
- Create: `.claude/commands/plan.md`
- Create: `.claude/commands/build.md`
- Create: `.claude/commands/test.md`
- Create: `.claude/commands/review.md`
- Create: `.claude/commands/debug.md`
- Create: `.claude/commands/ship.md`

- [ ] **Step 1: Create all 7 slash command files**

`.claude/commands/spec.md`:
```markdown
Read the skills `skills/idea-refine/SKILL.md` and `skills/spec-driven-development/SKILL.md`.

Follow the idea-refine skill first to explore and clarify the idea, then transition to spec-driven-development to produce a formal specification with acceptance criteria.

Context: The user wants to define what to build. Start by understanding their idea, then produce a spec.
```

`.claude/commands/plan.md`:
```markdown
Read the skill `skills/planning-and-task-breakdown/SKILL.md` and follow it exactly.

Break the current spec into small, verifiable tasks with acceptance criteria. Mark handoff-safe boundaries. Include Entire checkpoint markers and integration checkpoint points.

Context: The user has an approved spec and needs an implementation plan.
```

`.claude/commands/build.md`:
```markdown
Read the skill `skills/incremental-implementation/SKILL.md` and follow it exactly.

Implement the current plan task by task using thin vertical slices. Each slice must be tested before moving to the next. Track context budget. Follow plan mutation protocol if the plan needs to change.

Context: The user has an approved plan and wants to start building.
```

`.claude/commands/test.md`:
```markdown
Read the skill `skills/test-driven-development/SKILL.md` and follow it exactly.

Use the RED-GREEN-REFACTOR cycle. Write the failing test first, verify it fails, write minimal implementation, verify it passes, then refactor. Use Context7 to verify API behavior at the RED phase. If 3 consecutive attempts fail, trigger Codex rescue.

Context: The user wants to write or run tests using TDD.
```

`.claude/commands/review.md`:
```markdown
Read the skill `skills/code-review/SKILL.md` and follow it exactly.

Perform a five-axis code review: correctness, design, complexity, naming, and test coverage. For high-risk code (auth, payments, concurrency, public APIs), invoke Codex adversarial review.

Context: The user wants a code review before merging.
```

`.claude/commands/debug.md`:
```markdown
Read the skill `skills/debugging-and-error-recovery/SKILL.md` and follow it exactly.

Follow the five-step triage: reproduce, localize, hypothesize, fix, guard. Use Codex for parallel investigation. Follow the rollback protocol if 3 consecutive fix attempts fail on the same issue.

Context: Something is broken and the user needs systematic debugging.
```

`.claude/commands/ship.md`:
```markdown
Read the skills `skills/shipping-and-launch/SKILL.md` and `skills/context-handoff/SKILL.md`.

Follow the shipping skill first: run integration checkpoints, verify all tests pass, run Codex final review on the full diff, check documentation. Then follow context-handoff to archive the session.

Context: The user is ready to ship and close the session.
```

- [ ] **Step 2: Commit**

```bash
git add .claude/
git commit -m "feat: add 7 slash commands mapping to skill chains"
```

---

### Task 6: Agent Personas

**Files:**
- Create: `agents/code-reviewer.md`
- Create: `agents/test-engineer.md`
- Create: `agents/security-auditor.md`

- [ ] **Step 1: Write code-reviewer.md**

```markdown
# Code Reviewer

You are a Senior Staff Engineer performing code review. You've shipped production systems at scale and your reviews are known for catching design problems early.

## Review Priorities (in order)

1. **Correctness** — Does the code do what it claims? Are edge cases handled? Could it fail silently?
2. **Design** — Does the change fit the existing architecture? Are boundaries clean? Is the abstraction level right?
3. **Complexity** — Could someone else understand this without the author explaining it? Is there unnecessary indirection?
4. **Naming** — Do names communicate intent? Would a reader know what a function does from its name alone?
5. **Test Coverage** — Are the important behaviors tested? Are tests testing behavior, not implementation?

## What You Look For

- Functions doing too many things
- Abstraction layers that don't earn their complexity
- Error handling that swallows context
- Implicit coupling between components
- Changes that are larger than necessary for the stated goal
- Missing or weak test coverage on changed code paths
- Naming that requires reading the implementation to understand

## How You Communicate

- Lead with the most important issue
- Distinguish blocking issues from suggestions
- Provide concrete alternatives, not just criticism
- If the code is good, say so briefly — don't manufacture feedback
- Quantify impact when possible ("this allocates on every request" not "this might be slow")

## Integration

For high-risk code (auth, payments, concurrency, caching, public APIs, data migrations), recommend Codex adversarial review:
```
/codex:adversarial-review --background [specific concern]
```
```

- [ ] **Step 2: Write test-engineer.md**

```markdown
# Test Engineer

You are a QA Specialist who believes untested code is broken code you haven't found yet. You focus on test quality, not just test quantity.

## Review Priorities

1. **Coverage of changed code** — Every changed code path should have a test. Not just the happy path.
2. **Edge cases** — Empty inputs, null values, boundary conditions, error paths, concurrent access
3. **Test quality** — Tests should test behavior, not implementation. If you refactor the code, do the tests break for the wrong reason?
4. **Test naming** — Can you read the test name and know what it verifies without reading the test body?
5. **Test isolation** — Does each test stand alone? No order dependencies, no shared mutable state.

## What You Look For

- Happy-path-only testing
- Tests that mirror implementation (testing "how" not "what")
- Missing error path coverage
- Fragile tests that break on refactor
- Tests without clear assertions
- Shared test state that creates order dependencies
- Mock overuse that hides integration bugs
- Missing boundary condition tests

## Test Patterns You Enforce

- **Arrange-Act-Assert** — every test has these three phases, clearly separated
- **One behavior per test** — a test that checks five things tests nothing well
- **Descriptive names** — `test_expired_token_returns_401` not `test_auth_3`
- **Minimal fixtures** — create only what the test needs, nothing more

## How You Communicate

- Point to specific untested paths with file and line references
- Suggest concrete test cases, not just "add more tests"
- Distinguish critical coverage gaps from nice-to-have improvements
- Reference `references/testing-patterns.md` for detailed patterns
```

- [ ] **Step 3: Write security-auditor.md**

```markdown
# Security Auditor

You are a Security Engineer focused on preventing vulnerabilities before they ship. You think like an attacker reviewing the code.

## Review Priorities

1. **Input validation** — All external input (user, API, file) validated and sanitized before use
2. **Authentication & authorization** — Every endpoint checks identity AND permissions
3. **Data exposure** — No secrets in logs, error messages, or responses. No over-fetching.
4. **Injection** — SQL, command, XSS, template injection — parameterized queries, escaped output
5. **Configuration** — Security headers, CORS, CSP, cookie flags, TLS

## What You Look For

- Raw user input used in queries, commands, or templates
- Missing auth checks on new endpoints
- Secrets or PII in log output
- Overly permissive CORS or CSP policies
- Hardcoded credentials or API keys
- Missing rate limiting on authentication endpoints
- Session tokens stored insecurely
- Error messages that leak internal details
- Unvalidated redirects or file paths
- Missing CSRF protection on state-changing operations

## OWASP Top 10 Quick Check

For every change, mentally run through:
1. Broken access control?
2. Cryptographic failures?
3. Injection?
4. Insecure design?
5. Security misconfiguration?
6. Vulnerable components?
7. Auth failures?
8. Data integrity failures?
9. Logging failures?
10. SSRF?

## How You Communicate

- Classify findings: CRITICAL (blocks merge), HIGH (fix before ship), MEDIUM (fix soon), LOW (track)
- Explain the attack vector, not just the weakness
- Provide the secure alternative code, not just "fix this"
- Reference `references/security-checklist.md` for detailed checks
```

- [ ] **Step 4: Commit**

```bash
git add agents/
git commit -m "feat: add 3 agent personas — code reviewer, test engineer, security auditor"
```

---

### Task 7: Reference Checklists

**Files:**
- Create: `references/testing-patterns.md`
- Create: `references/security-checklist.md`
- Create: `references/performance-checklist.md`

- [ ] **Step 1: Write testing-patterns.md**

```markdown
# Testing Patterns Reference

Lookup checklist for test design. Referenced by skills — not a skill itself.

---

## Test Structure

### Arrange-Act-Assert

Every test follows three phases:

```
// Arrange — set up the preconditions
const user = createUser({ role: "admin" });
const request = buildRequest({ userId: user.id });

// Act — perform the action under test
const result = await handler(request);

// Assert — verify the outcome
expect(result.status).toBe(200);
expect(result.body.role).toBe("admin");
```

### One Behavior Per Test

Each test verifies exactly one behavior. If a test name needs "and" in it, split it.

Bad: `test_login_validates_email_and_checks_password_and_returns_token`
Good: Three separate tests for validation, password check, and token return.

## Naming Conventions

Format: `test_[unit]_[scenario]_[expected result]`

Examples:
- `test_login_with_expired_token_returns_401`
- `test_create_order_with_empty_cart_raises_validation_error`
- `test_calculate_tax_for_exempt_item_returns_zero`

## What to Test

| Priority | What | Example |
|----------|------|---------|
| Always | Happy path | Valid input → correct output |
| Always | Error paths | Invalid input → proper error |
| Always | Edge cases | Empty, null, boundary values |
| Always | Changed code paths | Every line you modified |
| When relevant | Concurrency | Race conditions, deadlocks |
| When relevant | Integration | Cross-service contracts |

## What NOT to Test

- Private implementation details (test the public interface)
- Third-party library internals (trust them or don't use them)
- Trivial getters/setters with no logic
- Framework behavior (test YOUR code, not the framework)

## Mocking Strategy

### Mock at boundaries, not everywhere

Mock:
- External APIs and services
- Databases (in unit tests — use real DBs in integration tests)
- File system, network, time

Don't mock:
- Your own internal modules (test them through the public API)
- Simple data transformations
- Anything you could use a real instance for cheaply

### Mock smell: if you're mocking more than 2 things, the unit under test is doing too much.

## Test Data

- Use factory functions, not raw object literals
- Create only what the test needs — no default-heavy fixtures
- Make test data obviously fake: `user@test.example.com`, not `john@gmail.com`
- Reset state between tests — no shared mutable state
```

- [ ] **Step 2: Write security-checklist.md**

```markdown
# Security Checklist Reference

Lookup checklist for security review. Referenced by skills — not a skill itself.

---

## Input Validation

- [ ] All user input validated on the server side (client validation is UX, not security)
- [ ] Input length limits enforced
- [ ] Input type/format validated (email, URL, date — use proven validators)
- [ ] File uploads: type whitelist, size limit, name sanitization, stored outside webroot
- [ ] Path traversal: reject `..`, normalize paths before use
- [ ] JSON/XML: parse with safe defaults (no external entities, no prototype pollution)

## Authentication

- [ ] Passwords hashed with bcrypt/scrypt/argon2 (never MD5/SHA)
- [ ] Rate limiting on login/registration endpoints
- [ ] Account lockout or progressive delays after failed attempts
- [ ] Session tokens: cryptographically random, sufficient length (>=128 bits)
- [ ] Tokens expire and can be revoked
- [ ] Password reset: time-limited, single-use tokens
- [ ] Multi-factor authentication available for sensitive operations

## Authorization

- [ ] Every endpoint checks both authentication AND authorization
- [ ] Default deny — require explicit grants, not explicit denials
- [ ] No client-side-only authorization checks
- [ ] Resource access validated: user can only access their own data
- [ ] Admin endpoints behind separate auth with audit logging
- [ ] API keys scoped to minimum required permissions

## Data Protection

- [ ] No secrets in source code, logs, or error messages
- [ ] PII encrypted at rest
- [ ] TLS for all data in transit
- [ ] Database queries parameterized (no string concatenation)
- [ ] API responses return only required fields (no over-fetching)
- [ ] Sensitive data masked in logs (credit cards, SSNs, tokens)

## HTTP Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## CORS

- [ ] Allowed origins explicitly listed (never `*` with credentials)
- [ ] Only required methods allowed
- [ ] Only required headers exposed
- [ ] Preflight cache configured (`Access-Control-Max-Age`)

## OWASP Top 10 Quick Reference

1. **Broken Access Control** — enforce server-side, default deny
2. **Cryptographic Failures** — TLS everywhere, strong hashing, no custom crypto
3. **Injection** — parameterized queries, escaped output, validated input
4. **Insecure Design** — threat model, abuse cases, defense in depth
5. **Security Misconfiguration** — minimal installs, no defaults, no verbose errors
6. **Vulnerable Components** — audit dependencies, patch quickly
7. **Auth Failures** — strong passwords, MFA, rate limits
8. **Data Integrity** — verify sources, sign updates, validate CI/CD
9. **Logging Failures** — log security events, alert on anomalies, no PII in logs
10. **SSRF** — validate URLs, whitelist destinations, block internal networks
```

- [ ] **Step 3: Write performance-checklist.md**

```markdown
# Performance Checklist Reference

Lookup checklist for performance review. Referenced by skills — not a skill itself.

---

## Measurement First

Never optimize without measurement. Profile, identify the bottleneck, fix it, measure again.

### Core Web Vitals

| Metric | Good | Needs Work | Poor |
|--------|------|-----------|------|
| LCP (Largest Contentful Paint) | ≤2.5s | ≤4.0s | >4.0s |
| INP (Interaction to Next Paint) | ≤200ms | ≤500ms | >500ms |
| CLS (Cumulative Layout Shift) | ≤0.1 | ≤0.25 | >0.25 |

### Measurement Commands

```bash
# Lighthouse (browser)
npx lighthouse https://url --output=json --output-path=report.json

# Bundle analysis
npx webpack-bundle-analyzer stats.json
npx source-map-explorer build/static/js/*.js

# Node.js profiling
node --prof app.js
node --prof-process isolate-*.log > profile.txt

# Database query analysis
EXPLAIN ANALYZE SELECT ...;
```

## Frontend Performance

- [ ] Images: modern formats (WebP/AVIF), responsive sizes, lazy loading below fold
- [ ] Fonts: `font-display: swap`, preload critical fonts, subset to used characters
- [ ] JavaScript: code-split by route, tree-shake unused exports, defer non-critical scripts
- [ ] CSS: purge unused styles, inline critical CSS, load rest async
- [ ] Caching: immutable assets with content hashes, appropriate Cache-Control headers
- [ ] Layout: dimensions on images/video to prevent CLS, avoid dynamic injection above fold

## Backend Performance

- [ ] Database: indexes on queried columns, avoid N+1 queries, use connection pooling
- [ ] Caching: cache expensive computations, cache external API responses, invalidation strategy
- [ ] Queries: select only needed columns, paginate large results, avoid full table scans
- [ ] APIs: support pagination, compress responses (gzip/brotli), set appropriate timeouts
- [ ] Async: offload slow operations to background jobs, use queues for heavy processing
- [ ] Memory: watch for leaks in long-running processes, profile allocation patterns

## When to Optimize

1. **Always:** algorithmic complexity (O(n^2) → O(n log n))
2. **Always:** obvious waste (loading unused data, redundant queries)
3. **Measure first:** micro-optimizations, caching, lazy loading
4. **Never:** premature optimization without measured bottleneck
```

- [ ] **Step 4: Commit**

```bash
git add references/
git commit -m "docs: add testing, security, and performance reference checklists"
```

---

### Task 8: Skill — idea-refine

**Files:**
- Create: `skills/idea-refine/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: idea-refine
description: Refines vague ideas into clear concepts through structured divergent and convergent thinking. Use when the user has an idea but hasn't defined what to build yet.
---

# Idea Refine

## Overview

Turn vague ideas into clear, actionable concepts before any specification work begins. This skill uses divergent thinking (explore possibilities) followed by convergent thinking (narrow to the best option) to ensure you're building the right thing.

## When to Use

- User says "I want to build..." or "What if we..." or "I have an idea for..."
- The request is too vague for a spec — you don't know the shape of the solution yet
- Multiple valid interpretations exist and the user hasn't committed to one

## When NOT to Use

- User has a clear spec or detailed requirements — go to `spec-driven-development`
- User is reporting a bug — go to `debugging-and-error-recovery`
- User knows exactly what to build and just needs a plan — go to `planning-and-task-breakdown`

## Integration Points

**Superpowers:** This skill maps to the Superpowers brainstorming phase. The output feeds directly into `spec-driven-development`.

**Context7:** When the idea involves specific libraries or APIs, use Context7 to verify feasibility before committing to an approach.

## Core Process

### 1. Understand the seed

Ask one question at a time. Start with:
- What problem does this solve? (not what does it do — why does it matter?)
- Who is it for?
- What does success look like?

Don't ask all three at once. One at a time, build understanding.

### 2. Diverge — explore the solution space

Generate 2-3 distinct approaches to the problem. Each should be genuinely different, not variations on one idea.

For each approach:
- One sentence: what is it?
- Key tradeoff: what do you gain and what do you give up?
- Feasibility: is this buildable with the current stack? (Context7 check if uncertain)

### 3. Converge — recommend and refine

Lead with your recommendation and explain why. Let the user challenge, redirect, or approve.

If the user pushes back, don't cave immediately — explain your reasoning. If they have better context, adapt. The goal is the best outcome, not agreement.

### 4. Lock the concept

Before exiting, confirm:
- One sentence: what are we building?
- One sentence: who is it for and what problem does it solve?
- One sentence: what is explicitly out of scope?

### 5. Transition to spec

Once the concept is locked, transition to `spec-driven-development` to formalize it.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "The user already knows what they want" | They know the problem. The solution space hasn't been explored. |
| "This is too small to need refinement" | Small ideas with unexamined assumptions cause the most rework. |
| "I can just start building and iterate" | Iterating without direction is expensive. 10 minutes of refinement saves hours. |
| "Let me gather context first" | This skill IS context gathering. Follow it. |

## Red Flags

- Jumping straight to implementation without exploring alternatives
- Asking zero clarifying questions before proposing a solution
- Proposing only one approach (no divergent thinking)
- Not locking the concept before moving to spec

## Verification

- [ ] At least 2 clarifying questions were asked
- [ ] At least 2 genuinely different approaches were explored
- [ ] A recommendation was given with reasoning
- [ ] The concept was locked in 3 sentences (what, who/why, not-scope)
- [ ] User approved the concept before transitioning
```

- [ ] **Step 2: Commit**

```bash
git add skills/idea-refine/
git commit -m "feat: add idea-refine skill — divergent/convergent thinking"
```

---

### Task 9: Skill — spec-driven-development

**Files:**
- Create: `skills/spec-driven-development/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: spec-driven-development
description: Produces formal specifications with acceptance criteria before any code is written. Use when starting a new feature, project, or significant change.
---

# Spec-Driven Development

## Overview

Define what you're building — with acceptance criteria — before writing a single line of code. A spec is a contract between the human (what and why) and the agent (how). Code without a spec is guessing.

## When to Use

- Starting a new feature, project, or significant change
- After `idea-refine` has locked a concept
- When requirements exist but aren't formalized
- Before any implementation work

## When NOT to Use

- Bug fixes with clear reproduction steps — go to `debugging-and-error-recovery`
- Trivial changes (rename, config tweak) — just do them
- You already have an approved spec — go to `planning-and-task-breakdown`

## Integration Points

**Context7:** Before committing to any library or API in the spec, verify it supports the required behavior. Run `resolve-library-id` + `query-docs` with specific queries (e.g., "Prisma findMany with nested include and pagination" not just "Prisma query"). Record confidence level.

**Superpowers:** This maps to the design phase of brainstorming. The output feeds into `planning-and-task-breakdown`.

## Core Process

### 1. Gather requirements

Read existing documentation, code, and context. If an `idea-refine` output exists, start from it.

Surface your assumptions:
```
ASSUMPTIONS I'M MAKING:
1. [requirement assumption]
2. [scope assumption]
3. [technical assumption]
→ Correct me now or I'll proceed with these.
```

### 2. Context7 feasibility check

For every library or API the spec will depend on:
1. `resolve-library-id(libraryName)` — get the Context7 ID
2. `query-docs(libraryId, specificQuery)` — verify the capability exists
3. Record: library, query, finding, installed version, confidence level

If confidence is LOW or NONE, flag it in the spec as a risk.

### 3. Write the spec

Structure:

```markdown
# [Feature Name] Specification

## Problem
What problem does this solve? Who has it? Why does it matter?

## Solution
What are we building? High-level description.

## Acceptance Criteria
Testable conditions that define "done":
- [ ] Given [context], when [action], then [result]
- [ ] Given [context], when [action], then [result]

## Non-Goals
What is explicitly OUT of scope.

## Technical Approach
Architecture, key decisions, libraries.

## Risks
What could go wrong. Include any LOW/NONE Context7 findings.

## Context7 Verifications
| Library | Query | Finding | Confidence |
|---------|-------|---------|------------|
```

### 4. Review with user

Present the spec. Ask:
- "Does this match what you have in mind?"
- "Anything missing from acceptance criteria?"
- "Anything in scope that shouldn't be?"

### 5. Lock the spec

Once approved, the spec is the contract. Changes go through plan mutation protocol.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "It's obvious what to build" | If it's obvious, the spec takes 5 minutes. If it's not, you just saved hours. |
| "The spec will slow us down" | Wrong specs are what slow you down. Writing one is fast. |
| "I'll define it as I go" | That's called scope creep. It's the #1 project killer. |
| "Requirements are clear from the code" | Code shows what IS, not what SHOULD BE. Specs define the target. |

## Red Flags

- Writing code before the spec is approved
- Acceptance criteria that aren't testable ("it should be fast")
- Missing non-goals section (scope will creep)
- No Context7 verification on library-dependent features
- Spec approved without user review

## Verification

- [ ] Problem statement is clear and specific
- [ ] Acceptance criteria are testable (Given/When/Then or equivalent)
- [ ] Non-goals section exists and is specific
- [ ] Context7 checks run for all library dependencies
- [ ] User has reviewed and approved the spec
- [ ] Spec saved to a file (not just in conversation)
```

- [ ] **Step 2: Commit**

```bash
git add skills/spec-driven-development/
git commit -m "feat: add spec-driven-development skill with Context7 feasibility checks"
```

---

### Task 10: Skill — planning-and-task-breakdown

**Files:**
- Create: `skills/planning-and-task-breakdown/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: planning-and-task-breakdown
description: Decomposes specs into small, verifiable tasks with acceptance criteria and handoff-safe boundaries. Use when you have an approved spec and need an implementation plan.
---

# Planning and Task Breakdown

## Overview

Break a spec into bite-sized tasks that can each be implemented, tested, and committed independently. Each task produces a working, verifiable increment. The plan marks handoff-safe boundaries so sessions can be interrupted cleanly.

## When to Use

- You have an approved spec from `spec-driven-development`
- The work requires more than one commit
- You need to coordinate multi-step implementation

## When NOT to Use

- No spec exists yet — go to `spec-driven-development`
- Single-commit change — just do it
- Bug fix — go to `debugging-and-error-recovery`

## Integration Points

**Entire:** Every commit creates an Entire checkpoint. The plan marks `--- HANDOFF SAFE ---` boundaries at points where the codebase is in a clean, deployable state.

**Superpowers:** This maps to the writing-plans phase. The plan feeds into `incremental-implementation`.

**Context7:** Verify every library method referenced in the plan. If a method doesn't exist or works differently than assumed, fix the plan now — not during implementation.

## Core Process

### 1. Read the spec

Load the full spec. Identify:
- All acceptance criteria (these become verification targets)
- Technical approach (this drives task decomposition)
- Risks (these may need spike tasks)

### 2. Decompose into tasks

Each task must:
- Be completable in one session (~30 min to 2 hours)
- Produce a working increment (tests pass before and after)
- Have clear acceptance criteria derived from the spec
- Be independently understandable (no "similar to task N")

Task format:
```markdown
### Task N: [Component/Feature]
**Files:** [exact paths — create/modify/test]
**Acceptance criteria:** [specific, testable]
**Depends on:** [task numbers, or "none"]
**Handoff safe after:** [yes/no]
```

### 3. Mark boundaries

Add `--- HANDOFF SAFE ---` after tasks where:
- All tests pass
- The codebase is in a deployable state
- A new session could resume without context loss

Add `--- INTEGRATION CHECKPOINT ---` after:
- Every 3-5 tasks
- Any task modifying shared interfaces (APIs, DB schema, auth, routing)

### 4. Verify the plan

- Does every acceptance criterion from the spec map to at least one task?
- Are dependencies between tasks explicit?
- Is any task too large (>2 hours)? Split it.
- Context7 check on every library method referenced.

### 5. Present for approval

Show the full plan. Get user approval before starting implementation.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I can figure out the tasks as I go" | That's how you end up with half-done features and broken tests. |
| "The plan will change anyway" | Plans change through mutation protocol, not by being absent. |
| "This is too small for a plan" | If it's more than one commit, it needs a plan. |
| "Breaking it down takes too long" | A 15-minute plan prevents hours of rework. |

## Red Flags

- Tasks without acceptance criteria
- Tasks with vague scope ("set up the backend")
- No handoff-safe boundaries marked
- Dependencies not explicit
- No integration checkpoints in a plan with >5 tasks
- Starting implementation before the plan is approved

## Verification

- [ ] Every spec acceptance criterion maps to at least one task
- [ ] Every task has specific acceptance criteria
- [ ] Dependencies between tasks are explicit
- [ ] Handoff-safe boundaries are marked
- [ ] Integration checkpoints are placed every 3-5 tasks
- [ ] Context7 verified library methods referenced in the plan
- [ ] No task exceeds ~2 hours estimated effort
- [ ] User has approved the plan
```

- [ ] **Step 2: Commit**

```bash
git add skills/planning-and-task-breakdown/
git commit -m "feat: add planning-and-task-breakdown skill with handoff boundaries"
```

---

### Task 11: Skill — incremental-implementation

**Files:**
- Create: `skills/incremental-implementation/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: incremental-implementation
description: Builds features through thin vertical slices, testing each before expanding. Use when implementing tasks from an approved plan.
---

# Incremental Implementation

## Overview

Build in thin vertical slices — each slice delivers a working, tested increment from input to output. Never build the full horizontal layer (all models, then all routes, then all UI). Instead, build one complete path through the stack, prove it works, then expand.

## When to Use

- You have an approved plan from `planning-and-task-breakdown`
- Building new features or significant changes
- Any implementation that touches multiple layers

## When NOT to Use

- Single-layer changes (just a CSS fix, just a config change)
- Bug fixes — go to `debugging-and-error-recovery`

## Integration Points

**Entire:** Commit after every slice. Each commit creates a checkpoint. If you need to roll back, you have a clean point.

**Context7:** Before implementing any library call, verify the API: `query-docs(libraryId, "specific method with args")`. Don't guess at signatures.

**Superpowers:** This runs inside the executing-plans or subagent-driven-development phase. Follow plan mutation protocol if the plan needs to change.

## Core Process

### 1. Load context

Read the plan task you're implementing. Load relevant files. Check CODEBASE.md for conventions and patterns.

Budget context: aim for <2000 lines of loaded context per task. More files does not mean better output.

### 2. Build the thinnest slice

Pick the simplest possible path through the feature:
- Hardcode what you can
- Skip error handling initially
- Use the simplest data (one item, not a list)
- Get it working end-to-end

### 3. Test the slice

Write a test that proves the slice works. Follow TDD if using `test-driven-development` skill (write test first, see it fail, implement, see it pass).

Run the test. It must pass.

### 4. Commit

```bash
git add [specific files]
git commit -m "feat: [what the slice does]"
```

### 5. Expand

Pick the next most valuable expansion:
- Replace hardcoded values with real logic
- Add error handling for the most likely failure
- Handle the next data case

Each expansion follows the same cycle: implement → test → commit.

### 6. Track budget

After each task, estimate context consumed:
- >5k tokens per remaining task: COMFORTABLE — continue
- 2k-5k: CAUTION — finish current task, evaluate handoff
- <2k: HANDOFF NOW — finish atomic unit, generate handoff

### 7. Plan mutation

If you discover the plan is wrong (task is harder than expected, dependency was missed, API doesn't work as planned):

1. Stop implementation
2. Document what changed and why
3. Update the plan with `[MUTATED]` markers
4. If >3 tasks affected, pause and present to user
5. Log in decisions-log.md

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "Let me build the full data layer first" | Horizontal layers are untestable in isolation. Build vertically. |
| "I'll add tests after" | Tests after are tests never. Test each slice. |
| "This slice is too small to commit" | Small commits are rollback points. Commit after every working increment. |
| "I'll handle errors at the end" | Error handling at the end means you don't know if it works. Add it incrementally. |

## Red Flags

- Building full horizontal layers before any vertical slice
- More than 30 minutes since last commit
- Multiple files changed without a test
- Context budget not tracked
- Plan changed without mutation protocol

## Verification

- [ ] Each slice produces a working increment
- [ ] Tests pass after every slice
- [ ] Commits happen after every working increment
- [ ] Context budget tracked throughout the session
- [ ] Plan mutations follow the formal protocol
```

- [ ] **Step 2: Commit**

```bash
git add skills/incremental-implementation/
git commit -m "feat: add incremental-implementation skill with context budget tracking"
```

---

### Task 12: Skill — test-driven-development

**Files:**
- Create: `skills/test-driven-development/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: test-driven-development
description: Enforces RED-GREEN-REFACTOR cycle — failing test first, then minimal implementation, then cleanup. Use when writing or running tests.
---

# Test-Driven Development

## Overview

Write the test before the code. Watch it fail (RED). Write the minimum code to make it pass (GREEN). Clean up (REFACTOR). This is not optional when this skill applies — the cycle enforces correctness.

## When to Use

- Implementing any new functionality
- Adding behavior to existing code
- Fixing a bug (write a test that reproduces it first)
- Used alongside `incremental-implementation`

## When NOT to Use

- Exploratory prototyping (throwaway code you'll delete)
- Pure configuration changes with no logic
- Documentation-only changes

## Integration Points

**Context7:** At the RED phase, before writing the test, verify the expected API behavior: `query-docs(libraryId, "specific method behavior")`. Don't write a test against an API you haven't verified.

**Codex:** If 3 consecutive attempts to make a test pass fail, trigger Codex rescue:
```
/codex:rescue --background [test path] fails with [error] — 3 attempts exhausted
```
Two models approaching the same problem from different angles.

**Entire:** Every GREEN → commit creates an Entire checkpoint. This is your rollback safety net.

## Core Process

### 1. RED — write the failing test

Before writing any implementation:

1. Context7 check: verify the API/behavior you're testing against
2. Write one test for one specific behavior
3. Name it descriptively: `test_[unit]_[scenario]_[expected]`
4. Use Arrange-Act-Assert structure
5. Run the test
6. **Confirm it fails for the right reason** — "function not defined" or "expected X got Y", NOT a syntax error or import error

If the test fails for the wrong reason, fix the test before proceeding.

### 2. GREEN — minimal implementation

Write the absolute minimum code to make the test pass:
- Hardcode return values if that's all the test requires
- Don't add logic the test doesn't demand
- Don't handle errors the test doesn't check
- Don't optimize

Run the test. It must pass. All existing tests must also pass.

### 3. REFACTOR — clean up

Now that you have a green test suite:
- Remove duplication
- Improve names
- Extract if complexity warrants it
- DO NOT change behavior (tests should still pass without modification)

Run all tests after refactoring. Everything must still pass.

### 4. COMMIT

```bash
git add [test file] [implementation file]
git commit -m "feat: [what behavior was added]"
```

### 5. Repeat

Next behavior → next test → RED → GREEN → REFACTOR → COMMIT.

### Failure Protocol

If a test won't pass after 3 attempts:

1. STOP — no more fix attempts
2. Check: is the test correct? (Context7 re-verify)
3. Check: is the approach correct? (re-read the plan)
4. If both correct, trigger Codex rescue
5. If the test is wrong, fix the test and reset the count

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I'll write tests after the implementation" | Tests written after are weaker — they test what you built, not what you intended. |
| "This is too simple to need TDD" | Simple code with no tests has no safety net for future changes. |
| "TDD is slower" | TDD is slower per line. It's faster per working feature. |
| "I know it works, I just ran it" | Manual testing is not repeatable. Write the test. |
| "The test is obvious, let me skip to GREEN" | The RED step catches wrong assumptions. Never skip it. |

## Red Flags

- Writing implementation before writing the test
- Test passes on first run (never saw RED)
- Test fails for the wrong reason and you proceed anyway
- Multiple behaviors tested in one test
- Refactoring changes behavior (tests modified during REFACTOR)
- More than 3 attempts to pass a test without escalation

## Verification

- [ ] Every new behavior has a test written BEFORE the implementation
- [ ] Every test was seen to fail (RED) before passing (GREEN)
- [ ] Tests fail for the RIGHT reason (not syntax/import errors)
- [ ] Refactoring doesn't change behavior (no test modifications in REFACTOR)
- [ ] All existing tests still pass after each cycle
- [ ] Commits happen after every GREEN → REFACTOR cycle
- [ ] Failure protocol triggered after 3 consecutive failures
```

- [ ] **Step 2: Commit**

```bash
git add skills/test-driven-development/
git commit -m "feat: add test-driven-development skill with Codex rescue protocol"
```

---

### Task 13: Skill — context-engineering

**Files:**
- Create: `skills/context-engineering/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: context-engineering
description: Manages what information is loaded into context and when, preventing both starvation and flooding. Use when starting a task, switching contexts, or when output quality degrades.
---

# Context Engineering

## Overview

Context is the single biggest lever for agent output quality. Too little and the agent hallucinates. Too much and it loses focus. This skill manages what information enters context, when, and how much.

## When to Use

- Starting any implementation session
- Output quality is degrading (hallucinated APIs, wrong patterns)
- Switching between tasks or areas of the codebase
- Setting up a new project or onboarding to an existing one

## When NOT to Use

- Already mid-task with good output quality — don't interrupt flow

## Integration Points

**Context7:** Primary tool for documentation verification. Use `resolve-library-id` + `query-docs` to load current, accurate library documentation instead of relying on training data.

**CODEBASE.md:** The `context-handoff` skill's CODEBASE-TEMPLATE.md defines the project map. This is the first file to load when starting a session.

## Core Process

### 1. Load the right context

Priority order for session start:
1. **Rules files** — CLAUDE.md (project conventions, always loaded)
2. **CODEBASE.md** — architecture, dependency map, conventions
3. **PLAN.md** — current task and its context
4. **HANDOFF.md** — previous session state (if resuming)
5. **Task-relevant source files** — only what this task touches

Target: <2000 lines of loaded context per task.

### 2. Brain dump pattern

When starting a new session or task, consolidate what the agent needs into one structured block:

```
CONTEXT FOR THIS TASK:
- Tech stack: [languages, frameworks, versions]
- What I'm building: [task description from plan]
- Key files: [paths]
- Patterns to follow: [from CODEBASE.md]
- Gotchas: [from FAILURES.md or known issues]
```

### 3. Refresh on task switch

When moving to a new task:
1. Mentally unload previous task's files
2. Load new task's relevant files
3. Re-read PLAN.md for the new task's context
4. Context7 check if the new task involves different libraries

### 4. Monitor for degradation

Watch for signs of context problems:

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Hallucinated APIs or methods | Context starvation | Context7 verify the API |
| Wrong patterns or conventions | Missing CODEBASE.md | Reload conventions |
| Repeating mistakes | FAILURES.md not loaded | Load failed approaches |
| Drifting from task | Context flooding | Reduce loaded files |
| Inconsistent naming | Stale context | Refresh from source files |

### 5. Context7 verification protocol

Before using any library API:
1. `resolve-library-id(libraryName)` — cache per session
2. `query-docs(libraryId, "specific method with args and version")` — be specific
3. Score confidence: HIGH (exact match) / MEDIUM (partial) / LOW (extrapolating) / NONE (no results)
4. If LOW/NONE: don't use it without manual verification

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I know this API well" | Training data may not reflect the installed version. Context7 check takes 10 seconds. |
| "More context is better" | More context means more noise. Load only what the task needs. |
| "I'll check the docs if something breaks" | Check BEFORE it breaks. Prevention is cheaper than debugging. |
| "This is boilerplate, I don't need context" | Boilerplate with wrong patterns causes the most insidious bugs. |

## Red Flags

- Starting implementation without loading CODEBASE.md
- Using library APIs without Context7 verification
- Loading >3000 lines of source files for a single task
- Not refreshing context when switching tasks
- Ignoring LOW/NONE confidence scores from Context7

## Verification

- [ ] CODEBASE.md loaded at session start
- [ ] Context7 used for every library API before implementation
- [ ] Task context under 2000 lines of source
- [ ] Context refreshed between task switches
- [ ] No hallucinated APIs in the implementation
```

- [ ] **Step 2: Commit**

```bash
git add skills/context-engineering/
git commit -m "feat: add context-engineering skill with Context7 verification protocol"
```

---

### Task 14: Skill — frontend-ui-engineering

**Files:**
- Create: `skills/frontend-ui-engineering/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: frontend-ui-engineering
description: Builds production-quality UI with accessibility, responsive design, and component architecture. Use when implementing user-facing interfaces.
---

# Frontend UI Engineering

## Overview

Build UI that works for everyone — accessible, responsive, performant, and maintainable. Components should be composable, testable, and follow the project's design system.

## When to Use

- Implementing user-facing interfaces
- Creating or modifying UI components
- Working with CSS, layouts, or responsive design
- Building forms, navigation, or interactive elements

## When NOT to Use

- Backend-only changes
- CLI tools or scripts
- API-only work — go to `incremental-implementation`

## Integration Points

**Context7:** Verify component APIs and framework patterns before implementing. Frontend frameworks change rapidly — don't trust training data for version-specific syntax.

## Core Process

### 1. Understand the component

Before building:
- What is the component's single responsibility?
- What are its inputs (props) and outputs (events/callbacks)?
- What states can it be in? (loading, empty, error, populated, disabled)
- What accessibility requirements apply?

### 2. Start with semantic HTML

Write the markup first, without styling:
- Use semantic elements (`<nav>`, `<main>`, `<article>`, `<button>`, not `<div>` for everything)
- Add ARIA attributes where semantics alone aren't sufficient
- Ensure keyboard navigation works before adding any JavaScript

### 3. Add styles

Follow the project's CSS approach (modules, Tailwind, styled-components, etc.). Check CODEBASE.md.

Responsive design:
- Mobile-first: start with the smallest viewport, add complexity for larger
- Use relative units (rem, %, viewport units) over fixed pixels
- Test at: 320px, 768px, 1024px, 1440px minimum

### 4. Add behavior

Interactive elements:
- All interactive elements reachable by keyboard (Tab, Enter, Escape, Arrow keys)
- Focus management: focus moves logically, focus traps in modals
- Loading states: show feedback immediately, not after a delay
- Error states: clear, actionable error messages near the relevant input

### 5. Accessibility checklist

Every component must pass:
- [ ] Color contrast ratio ≥ 4.5:1 for normal text, ≥ 3:1 for large text
- [ ] All images have meaningful alt text (or empty alt="" for decorative)
- [ ] Form inputs have visible labels (not just placeholders)
- [ ] Interactive elements have minimum 44x44px touch targets
- [ ] Component works with screen reader (meaningful announcements)
- [ ] No content conveyed by color alone
- [ ] Animations respect `prefers-reduced-motion`

### 6. Test in browser

Start the dev server. Verify:
- Golden path works as expected
- Edge cases (empty state, long content, error state)
- Responsive behavior at multiple viewport sizes
- Keyboard navigation
- No console errors

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I'll add accessibility later" | Accessibility retrofits cost 10x more than building it in. |
| "It works on desktop, we'll fix mobile later" | Mobile-first is easier to expand up than desktop-first is to shrink down. |
| "The tests pass, it must look right" | Tests verify logic, not visual correctness. Look at it in a browser. |
| "Divs with click handlers work fine" | They don't. Use buttons for actions, links for navigation. Keyboard and screen reader users can't use click-handler divs. |

## Red Flags

- `<div onClick>` instead of `<button>`
- No alt text on images
- Fixed pixel widths on containers
- No loading or error states
- No keyboard navigation support
- Never tested in a browser during development
- Inline styles everywhere instead of following project CSS patterns

## Verification

- [ ] Semantic HTML used (no `<div>` soup)
- [ ] Accessibility checklist passes
- [ ] Responsive at 320px, 768px, 1024px, 1440px
- [ ] Keyboard navigation works for all interactive elements
- [ ] Loading, empty, and error states handled
- [ ] Tested in browser — golden path and edge cases
- [ ] CSS follows project conventions (from CODEBASE.md)
```

- [ ] **Step 2: Commit**

```bash
git add skills/frontend-ui-engineering/
git commit -m "feat: add frontend-ui-engineering skill with accessibility checklist"
```

---

### Task 15: Skill — debugging-and-error-recovery

**Files:**
- Create: `skills/debugging-and-error-recovery/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: debugging-and-error-recovery
description: Systematic five-step triage for bugs and failures with rollback protocol. Use when something is broken or behaving unexpectedly.
---

# Debugging and Error Recovery

## Overview

Reproduce, localize, hypothesize, fix, guard. Don't guess — trace. Don't shotgun — isolate. Every fix gets a regression test. If 3 fix attempts fail, roll back to the last known-good state.

## When to Use

- Tests are failing
- Runtime errors or unexpected behavior
- Build failures
- Something that used to work doesn't anymore

## When NOT to Use

- New feature development — go to `incremental-implementation`
- Code quality issues without bugs — go to `code-simplification`

## Integration Points

**Codex:** For complex bugs, run parallel investigation:
```
/codex:rescue --background investigate why [specific test/behavior] fails
```
Two models approaching the same bug from different angles.

**Entire:** Checkpoints are your rollback points. Every commit before the bug is a known-good state you can return to.

**Context7:** If the hypothesis involves library behavior, verify with Context7 before attempting a fix. Don't fix code that's actually correct — the library might have changed.

## Core Process

### 1. REPRODUCE

Before anything else, reproduce the bug reliably:
- What is the exact error message or unexpected behavior?
- What is the exact command or action that triggers it?
- Can you reproduce it consistently?

If you can't reproduce it, you can't fix it. Investigate environmental differences.

### 2. LOCALIZE

Narrow the search space:
- When did it last work? (`git log`, `git bisect`)
- What changed between working and broken? (`git diff`)
- Where does the error originate? (read the stack trace — the FULL trace, not just the first line)
- Add logging or breakpoints to trace the execution path

### 3. HYPOTHESIZE

Form a specific, testable hypothesis:
```
HYPOTHESIS: [specific claim about what's wrong]
TEST: [how to confirm or refute]
EXPECTED if correct: [what you'll see]
EXPECTED if wrong: [what you'll see instead]
```

If the hypothesis involves a library: Context7 check before testing.

### 4. FIX

Apply the minimum change that addresses the root cause:
- Don't fix symptoms — fix causes
- Don't refactor while fixing — scope discipline
- Don't change unrelated code

### 5. GUARD

Write a regression test that:
- Reproduces the original bug (would have failed before the fix)
- Passes with the fix in place
- Prevents the same bug from recurring

Run the full test suite. No new failures.

### Rollback Protocol

**Trigger: 3 consecutive fix attempts on the same failure.**

1. STOP — no more fix attempts
2. Find the last green checkpoint: `git log` or `entire explain`
3. Revert: `git reset --hard <last-green-SHA>`
4. Log in FAILURES.md:
   - What was tried
   - Why it failed
   - Time spent
   - Checkpoint ID
5. Re-approach from clean state with a different strategy
6. If same task fails 2x with different approaches: escalate to user or `/codex:rescue`

### Failure Escalation

```
1 failure  → normal debugging
2 failures → re-read hypothesis, Context7 verify
3 failures → ROLLBACK + different approach
2 rollbacks on same task → BLOCKED → escalate
```

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I know what's wrong, let me just fix it" | Hypothesis without reproduction is guessing. Reproduce first. |
| "Let me try a quick fix" | Quick fixes without diagnosis create new bugs. Follow the process. |
| "The error message is clear enough" | Read the FULL stack trace. The real cause is often 5 frames deep. |
| "I'll skip the regression test, the fix is obvious" | Obvious fixes without tests regress. Every fix gets a test. |
| "One more attempt will work" | That's attempt #4. Rollback protocol exists for a reason. |

## Red Flags

- Fixing without reproducing first
- Reading only the first line of a stack trace
- Changing multiple things at once (can't tell which fixed it)
- No regression test for the fix
- More than 3 fix attempts without rollback
- Fixing symptoms instead of root cause

## Verification

- [ ] Bug was reproduced reliably before attempting a fix
- [ ] Root cause was localized (not just symptoms)
- [ ] Hypothesis was explicitly stated before fix attempt
- [ ] Fix addresses root cause, not symptoms
- [ ] Regression test written and passing
- [ ] Full test suite passes (no new failures)
- [ ] Rollback protocol followed if 3 attempts failed
```

- [ ] **Step 2: Commit**

```bash
git add skills/debugging-and-error-recovery/
git commit -m "feat: add debugging-and-error-recovery skill with rollback protocol"
```

---

### Task 16: Skill — code-review

**Files:**
- Create: `skills/code-review/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: code-review
description: Five-axis code review with Codex adversarial review for high-risk code. Use when reviewing code before merge.
---

# Code Review

## Overview

Review code across five axes: correctness, design, complexity, naming, and test coverage. For high-risk code, add a cross-model adversarial review via Codex. The goal is catching problems before they ship, not gatekeeping.

## When to Use

- Before merging any feature branch
- After completing implementation tasks
- When requested by user (`/review`)
- At integration checkpoints

## When NOT to Use

- During active implementation — finish the task first
- For exploratory/throwaway code

## Integration Points

**Codex:** For high-risk code (auth, payments, concurrency, caching, public APIs, data migrations), run adversarial review:
```
/codex:adversarial-review --background [specific concern]
```
For full branch review before merge:
```
/codex:review --base main --background
```

**Entire:** The review happens at a checkpoint boundary. If the review finds issues, fixes go in new commits (not amendments).

## Core Process

### 1. Understand the change

Read the spec/plan for context. Then read the diff:
```bash
git diff main...HEAD
```
Understand WHAT changed and WHY before evaluating HOW.

### 2. Five-axis review

**Correctness:**
- Does the code do what the spec requires?
- Are edge cases handled?
- Could it fail silently? (error swallowed, default value hiding a problem)
- Are race conditions possible?

**Design:**
- Does it fit the existing architecture? (check CODEBASE.md)
- Are boundaries clean? (clear inputs/outputs, minimal coupling)
- Is the abstraction level appropriate? (not too abstract, not too concrete)
- Chesterton's Fence: if you don't understand why something exists, don't remove it

**Complexity:**
- Could someone understand this without the author explaining it?
- Is there unnecessary indirection?
- Are there simpler alternatives that achieve the same result?

**Naming:**
- Do names communicate intent?
- Consistent with codebase conventions?
- No abbreviations that require explanation?

**Test coverage:**
- Are changed code paths tested?
- Do tests verify behavior (not implementation)?
- Are edge cases in the tests?
- See `references/testing-patterns.md`

### 3. Classify findings

| Severity | Meaning | Example |
|----------|---------|---------|
| **BLOCKING** | Must fix before merge | Missing auth check, data loss risk |
| **SHOULD FIX** | Fix before ship | Poor error message, missing edge case test |
| **NIT** | Improve if convenient | Naming suggestion, style preference |

### 4. High-risk check

If the change touches any of these, trigger Codex adversarial review:
- Authentication or authorization
- Payment processing
- Concurrency or shared state
- Caching (invalidation is hard)
- Public API contracts
- Data migrations
- Cryptographic operations

### 5. Summarize

```
## Review Summary
- Overall: APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
- Blocking issues: [count]
- Should-fix issues: [count]
- Nits: [count]
- Codex review: [requested / not needed]
```

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "The tests pass, so it's correct" | Tests verify tested paths. They don't prove the absence of bugs. |
| "I wrote it, I know it's fine" | Self-review catches ~30% of issues. Fresh eyes catch more. |
| "This is simple, it doesn't need review" | Simple code with subtle bugs is the hardest to debug later. |
| "Codex review is overkill" | For auth and payments, overkill is the right amount. |

## Red Flags

- Reviewing only the happy path
- No test coverage check on changed lines
- Skipping Codex adversarial review on high-risk code
- Approving without understanding the full change
- All feedback is nits, no structural review

## Verification

- [ ] Full diff reviewed (not just new files)
- [ ] All five axes checked (correctness, design, complexity, naming, tests)
- [ ] Findings classified by severity
- [ ] High-risk code triggers Codex adversarial review
- [ ] Review summary provided with clear recommendation
```

- [ ] **Step 2: Commit**

```bash
git add skills/code-review/
git commit -m "feat: add code-review skill with five-axis review and Codex adversarial"
```

---

### Task 17: Skill — code-simplification

**Files:**
- Create: `skills/code-simplification/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: code-simplification
description: Reduces code complexity by removing unnecessary abstractions and indirection. Use when code is harder to understand than the problem it solves.
---

# Code Simplification

## Overview

Complexity is a cost. Every abstraction, layer, and indirection must earn its place. This skill identifies unnecessary complexity and removes it while preserving behavior. The goal is code that's as simple as the problem allows — no simpler.

## When to Use

- Code is harder to read than the problem it solves
- After a feature is complete and working (simplify as a separate step)
- When reviewing code that feels over-engineered
- When a file has grown too large and does too many things

## When NOT to Use

- During initial implementation — get it working first, then simplify
- When you don't understand the code — understand before changing
- Chesterton's Fence: if you don't know why something exists, don't remove it

## Core Process

### 1. Understand before changing

Read the code. Understand what it does and why. If anything is unclear, investigate — check git blame, read tests, check for callers.

Never simplify code you don't fully understand.

### 2. Identify complexity

Look for:
- **Dead code** — unreachable branches, unused functions, commented-out blocks
- **Unnecessary abstraction** — interfaces with one implementation, base classes with one child, factories that create one thing
- **Premature generalization** — config options nobody uses, plugin systems with no plugins, generic code for one use case
- **Indirection without value** — wrapper functions that just call another function, delegation chains
- **Feature flags for shipped features** — clean them up

### 3. Apply Chesterton's Fence

Before removing anything:
- Why was this added? (git blame + commit message)
- Is it still needed? (grep for callers/references)
- If you can't determine why, don't remove it — ask

### 4. Simplify

One change at a time:
1. Remove the simplest unnecessary thing
2. Run tests — all must pass
3. Commit
4. Repeat

Never simplify multiple things in one commit. If a simplification breaks tests, you need to know exactly which change caused it.

### 5. Verify behavior preserved

After all simplifications:
- All existing tests pass without modification
- No new behavior added (simplification is not enhancement)
- The code does exactly what it did before, just more simply

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "We might need this flexibility later" | YAGNI. Remove it. Add it back when you actually need it. |
| "The abstraction makes it cleaner" | Abstraction with one consumer is not cleaner — it's indirection. |
| "I'll just clean up everything while I'm here" | Scope discipline. Simplify the code you're working on, not adjacent code. |
| "This refactor is too risky without tests" | Then write the tests first. Don't leave complexity because testing is hard. |

## Red Flags

- Simplifying code you don't understand
- Removing code without checking for callers
- Multiple simplifications in one commit
- Tests modified during simplification (behavior changed)
- Scope expanding beyond the target code

## Verification

- [ ] Code was fully understood before changes
- [ ] Chesterton's Fence checked for every removal
- [ ] Each simplification is in its own commit
- [ ] All existing tests pass without modification
- [ ] No behavior change (simplification, not enhancement)
- [ ] Code is genuinely simpler (fewer lines, fewer abstractions, fewer indirections)
```

- [ ] **Step 2: Commit**

```bash
git add skills/code-simplification/
git commit -m "feat: add code-simplification skill with Chesterton's Fence checks"
```

---

### Task 18: Skill — shipping-and-launch

**Files:**
- Create: `skills/shipping-and-launch/SKILL.md`

- [ ] **Step 1: Write SKILL.md**

```markdown
---
name: shipping-and-launch
description: Pre-launch verification, integration checkpoints, and deployment procedures. Use when ready to ship a feature or close out an epic.
---

# Shipping and Launch

## Overview

Ship with confidence. Before any code leaves the branch, run the full integration checkpoint, get a cross-model review, verify documentation, and confirm rollback procedures. Shipping is a process, not a moment.

## When to Use

- Feature implementation is complete
- All plan tasks are done
- Ready to merge and/or deploy
- Closing out an epic

## When NOT to Use

- Still implementing — finish first
- Tests failing — fix first

## Integration Points

**Entire:** All documentation generated, session archived. Entire checkpoints provide the full history.

**Codex:** Final review on the full branch diff:
```
/codex:review --base main --background
```

**Context7:** Final verification on any library APIs used — confirm installed versions match what was developed against.

## Core Process

### 1. Integration checkpoint

Run the full validation suite:

```
- [ ] All tests pass (full suite, not just new tests)
- [ ] Build succeeds
- [ ] Lint/format passes
- [ ] Cross-system dependencies checked (from CODEBASE.md):
      - API contracts match?
      - DB migrations consistent?
      - Third-party integrations work?
      - Environment variables present?
```

### 2. Codex final review

Request cross-model review on the full diff:
```
/codex:review --base main --background
```

While waiting for Codex, continue with steps 3-5.

### 3. Documentation check

- [ ] README updated if features added
- [ ] API documentation for new endpoints
- [ ] CODEBASE.md updated if architecture changed
- [ ] Migration guide if breaking changes
- [ ] Inline comments match codebase style

### 4. Pre-launch checklist

- [ ] All acceptance criteria from spec are met
- [ ] No TODO/FIXME/HACK comments in shipped code
- [ ] No console.log/print debugging statements left
- [ ] No hardcoded test/development values
- [ ] Error messages are user-friendly (no stack traces to end users)
- [ ] Feature flags configured correctly (if applicable)

### 5. Rollback plan

Before deploying, document:
- How to roll back (specific commands or procedures)
- What data might be affected (migrations, caches)
- Who to notify if rollback is needed
- How to verify the rollback worked

### 6. Review Codex results

Check `/codex:status` and `/codex:result`. Address any findings:
- CRITICAL: fix before merge
- HIGH: fix before deploy
- MEDIUM: create follow-up ticket
- LOW: note for next session

### 7. Merge and tag

```bash
git checkout main
git merge --no-ff feature-branch
git tag -a v[version] -m "[description]"
```

### 8. Transition to context-handoff

After shipping, invoke `context-handoff` to archive the session and prepare the handoff document.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "Tests pass, ship it" | Tests are necessary but not sufficient. Integration checkpoint catches what unit tests miss. |
| "We can fix documentation later" | Documentation debt compounds. Update it now while context is fresh. |
| "Codex review is too slow" | Start it early (step 2) and continue with other checks while it runs. |
| "We don't need a rollback plan" | You need a rollback plan precisely when you think you don't. |

## Red Flags

- Shipping without running the full test suite
- No Codex review on multi-day features
- Documentation not updated
- No rollback plan
- TODO/FIXME left in shipped code
- Skipping context-handoff after shipping

## Verification

- [ ] Full integration checkpoint passed
- [ ] Codex review requested and findings addressed
- [ ] Documentation updated
- [ ] Pre-launch checklist completed
- [ ] Rollback plan documented
- [ ] Context-handoff invoked to archive the session
```

- [ ] **Step 2: Commit**

```bash
git add skills/shipping-and-launch/
git commit -m "feat: add shipping-and-launch skill with integration checkpoints"
```

---

### Task 19: Docs

**Files:**
- Create: `docs/getting-started.md`
- Create: `docs/skill-anatomy.md`

- [ ] **Step 1: Write getting-started.md**

```markdown
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
4. The skills chain naturally: spec → plan → build → test → review → ship
```

- [ ] **Step 2: Write skill-anatomy.md**

```markdown
# Skill Anatomy

How to write a new skill for neo-skills. Every skill follows the same structure.

## Directory Structure

```
skills/
  skill-name/         # kebab-case directory name
    SKILL.md           # Required: skill definition
    REFERENCE.md       # Optional: detailed reference material
    INTEGRATIONS.md    # Optional: integration-specific details
```

## SKILL.md Format

Every SKILL.md requires YAML frontmatter and these sections:

```markdown
---
name: skill-name
description: One sentence. What it does + when to trigger it.
---

# Skill Title

## Overview
2-3 sentences. What this skill does and why it matters.

## When to Use
- Specific triggering conditions
- Be concrete: "when the user says X" or "when Y is true"

## When NOT to Use
- Exclusions with redirects to the correct skill

## Integration Points
How this skill connects to Superpowers, Entire, Context7, and/or Codex.
Only include integrations that are relevant to this skill.

## Core Process
Numbered step-by-step workflow. This is the meat of the skill.
Each step must be actionable — not advice, but instructions.
Include verification gates: no step is "done" until evidence proves it.

## Common Rationalizations
| Rationalization | Reality |
|----------------|---------|
| "Agent excuse" | Why the excuse is wrong |

This table prevents agents from skipping steps. Include 4-6 entries
covering the most common shortcuts an agent might take.

## Red Flags
Observable signs the skill isn't being followed. Bullet list.

## Verification
Checklist with evidence requirements. Every item should be
verifiable with output, not just "seems right."
```

## Key Principles

### Process over knowledge

Skills are workflows, not reference docs. Every section should tell the agent what to DO, not what to KNOW. Reference material belongs in supporting files or the `references/` directory.

### Under 500 lines

Keep SKILL.md under 500 lines. If reference material exceeds ~100 lines, put it in a supporting file (REFERENCE.md, INTEGRATIONS.md) and link to it from SKILL.md.

### Specific descriptions

The `description` field in frontmatter is how agents decide whether to activate a skill. It must communicate both WHAT the skill does and WHEN to use it. Bad: "Helps with testing." Good: "Enforces RED-GREEN-REFACTOR cycle — failing test first, then minimal implementation, then cleanup. Use when writing or running tests."

### Common Rationalizations are essential

This is the most important innovation from agent-skills. Agents will rationalize skipping steps. The rationalizations table preempts this by pairing excuses with counter-arguments. Write entries based on actual observed agent behavior.

### Verification requires evidence

"Seems right" is never sufficient. Every verification item should be checkable with concrete evidence: test output, file existence, command output, git log.

## Supporting Files

Use supporting files when:
- Reference material exceeds ~100 lines
- Integration details are complex enough to need their own file
- Templates or examples are large

Naming: UPPERCASE.md (REFERENCE.md, INTEGRATIONS.md, TEMPLATE.md)

## Testing a Skill

1. Create a test project with a task that should trigger the skill
2. Verify the skill activates when expected
3. Follow the skill workflow completely
4. Check that verification criteria can be objectively assessed
5. Have someone unfamiliar with the skill try to follow it — if they get stuck, the skill needs clarification
```

- [ ] **Step 3: Commit**

```bash
git add docs/getting-started.md docs/skill-anatomy.md
git commit -m "docs: add getting-started guide and skill anatomy reference"
```

---

### Task 20: README.md

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write README.md**

```markdown
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
| `/spec` | idea-refine → spec-driven-development |
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
| **Superpowers** | Development rhythm (brainstorm → plan → execute → review) | Claude Code plugin |
| **Entire** | Session recording, checkpoints, crash recovery | `npm i -g @anthropic/entire-cli` |
| **Context7** | Documentation verification with confidence scoring | `claude mcp add context7` |
| **Codex** | Cross-model review, parallel debugging, delegation | `/plugin marketplace add openai/codex-plugin-cc` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add or modify skills.

## License

[MIT](LICENSE)
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with skill catalog and quick start guide"
```

---

### Task 21: CONTRIBUTING.md

**Files:**
- Create: `CONTRIBUTING.md`

- [ ] **Step 1: Write CONTRIBUTING.md**

```markdown
# Contributing to neo-skills

## Adding a New Skill

### 1. Create the skill directory

```bash
mkdir -p skills/your-skill-name
```

Use kebab-case for the directory name.

### 2. Write SKILL.md

Follow the format in [docs/skill-anatomy.md](docs/skill-anatomy.md). Every skill requires:

- YAML frontmatter with `name` and `description`
- Overview (2-3 sentences)
- When to Use / When NOT to Use
- Integration Points (only relevant integrations)
- Core Process (numbered, actionable steps with verification gates)
- Common Rationalizations table (4-6 entries)
- Red Flags (bullet list)
- Verification checklist (evidence-based)

### 3. Keep it under 500 lines

The main SKILL.md should be under 500 lines. Put reference material in supporting files:

- `REFERENCE.md` — detailed protocols or lookup tables
- `INTEGRATIONS.md` — complex integration details
- Templates or examples in their own files

### 4. Test it

1. Create a test project with a task that should trigger your skill
2. Follow the skill workflow completely
3. Verify that every verification criterion can be objectively assessed
4. Check that the Common Rationalizations table addresses real agent shortcuts

### 5. Submit a PR

- One skill per PR
- Include a description of when the skill should be triggered
- Include an example of the skill being used (if possible)

## Modifying an Existing Skill

- Keep changes focused — don't restructure while fixing a bug
- If changing the Core Process, verify the Verification checklist still matches
- Update Common Rationalizations if you've observed new agent shortcuts

## Style Guidelines

- Skills are workflows, not reference docs — tell agents what to DO
- Be specific and concrete — "run `npm test`" not "run tests"
- Verification requires evidence — "seems right" is never sufficient
- Use tables for structured comparisons (rationalizations, severity levels)
- Write in second person imperative ("Write the test", not "The test should be written")

## What NOT to Contribute

- Reference docs that don't encode a workflow — put these in `references/`
- Platform-specific setup — this repo targets Claude Code only
- Skills that duplicate existing skills — extend them instead
```

- [ ] **Step 2: Commit**

```bash
git add CONTRIBUTING.md
git commit -m "docs: add CONTRIBUTING.md with skill creation guidelines"
```

---

## Plan Summary

| Task | Description | Files |
|------|-------------|-------|
| 1 | Repository foundation | .gitignore, LICENSE |
| 2 | CLAUDE.md | CLAUDE.md |
| 3 | AGENTS.md | AGENTS.md |
| 4 | Move context-handoff | 5 files moved to skills/context-handoff/ |
| 5 | Slash commands | 7 files in .claude/commands/ |
| 6 | Agent personas | 3 files in agents/ |
| 7 | Reference checklists | 3 files in references/ |
| 8 | Skill: idea-refine | skills/idea-refine/SKILL.md |
| 9 | Skill: spec-driven-development | skills/spec-driven-development/SKILL.md |
| 10 | Skill: planning-and-task-breakdown | skills/planning-and-task-breakdown/SKILL.md |
| 11 | Skill: incremental-implementation | skills/incremental-implementation/SKILL.md |
| 12 | Skill: test-driven-development | skills/test-driven-development/SKILL.md |
| 13 | Skill: context-engineering | skills/context-engineering/SKILL.md |
| 14 | Skill: frontend-ui-engineering | skills/frontend-ui-engineering/SKILL.md |
| 15 | Skill: debugging-and-error-recovery | skills/debugging-and-error-recovery/SKILL.md |
| 16 | Skill: code-review | skills/code-review/SKILL.md |
| 17 | Skill: code-simplification | skills/code-simplification/SKILL.md |
| 18 | Skill: shipping-and-launch | skills/shipping-and-launch/SKILL.md |
| 19 | Docs | docs/getting-started.md, docs/skill-anatomy.md |
| 20 | README | README.md |
| 21 | CONTRIBUTING | CONTRIBUTING.md |

**Total: 21 tasks, ~35 files created/moved**
