Good — I have the skill loaded and the real package.json in hand. Let me walk you through Phase 0 and give you a running start into Phase 1, grounded in your actual dependencies. I'll also flag where the Context7 MCP server needs to be wired up (per the user memory note) before we can finalize the dependency audit.

---

# Welcome to the Context Handoff Workflow

You're at **Phase 0 — Codebase Understanding** on the `acme-billing` repo. Before we touch a line of code, the skill requires we:

1. Map the codebase (CODEBASE.md)
2. Frame the epic (EPIC.md)
3. Design the plan (PLAN.md)
4. Then — and only then — open a worktree and start TDD

The skill's design principle: **context is the bottleneck, not speed.** One hour of Phase 0 saves ten hours of dead-end refactors.

---

## Setup checks before we proceed

A few environment preconditions the skill expects. Please confirm/run these so our handoff is usable next session:

```bash
# Entire — session recording
entire status
# If not installed: npm install -g @anthropic/entire-cli && entire enable --agent claude-code

# Context7 — doc verification (MCP)
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
# Per your memory note, Context7 is currently not configured — this blocks the dependency audit.

# Codex — cross-model review (optional but recommended for Stripe/auth code)
/plugin install codex@openai-codex && /codex:setup

# Repo-local memory directory
mkdir -p .agent-memory/handoff-archive
```

Once Context7 is wired up, we re-run the dependency audit block in CODEBASE.md. Until then I'll mark those rows `NONE (Context7 unavailable)` rather than guess.

---

## Phase 0: CODEBASE.md (initial draft from package.json)

This is a **first-pass skeleton**. Sections marked `{{INVESTIGATE}}` need me to actually read the repo (prisma/schema.prisma, app directory, existing API routes, auth config). Treat this as the scaffolding we fill in during the rest of Phase 0.

```markdown
# Codebase: acme-billing
Last updated: 2026-04-21 — Session 1

## Architecture

acme-billing is a Next.js 15.1.0 App Router application using Prisma 5.22.0 as its
ORM, Stripe 17.2.0 for payment processing, and NextAuth v5 (5.0.0-beta.25) on top
of @auth/core 0.37.0 for authentication. ioredis 5.4.1 is present, suggesting
Redis-backed caching, rate limiting, or session storage. Zod 3.23.8 handles
runtime schema validation — expect it at API route boundaries and form handlers.

Entry points are driven by Next.js conventions: `app/` for routes and RSC, with
Stripe webhooks almost certainly under `app/api/webhooks/stripe/route.ts` (to
verify). Prisma generates the typed client into `node_modules/@prisma/client` and
the schema lives at `prisma/schema.prisma`. Migrations are applied via
`npm run db:migrate` (`prisma migrate dev`), and database seeding runs through a
TypeScript seed file executed with `tsx` (`prisma/seed.ts`).

Data flow for the forthcoming subscription work will cross four systems:
browser → Next.js route handler → Stripe API (outbound) and Stripe webhooks
(inbound) → Prisma → Postgres, with NextAuth mediating the "who is this user"
question and ioredis likely doing idempotency/rate-limiting duty.

## File Structure

{{INVESTIGATE — confirm by reading repo; this is the expected Next.js + Prisma layout}}

## Cross-System Dependency Map

| System     | Type           | Interface                 | Contract                       | Health Check                          |
|------------|----------------|---------------------------|--------------------------------|---------------------------------------|
| PostgreSQL | Database       | Prisma 5.22.0             | schema.prisma (version TBD)    | `prisma migrate status`               |
| Stripe     | Payment API    | stripe-node 17.2.0 + webhooks | apiVersion pinned in client | `stripe.customers.list({limit:1})` in test mode |
| Redis      | Cache/queue    | ioredis 5.4.1             | standalone (confirm in config) | `redis.ping()`                        |
| NextAuth   | Auth provider  | @auth/core 0.37.0 + next-auth 5.0.0-beta.25 | JWT or DB sessions (TBD) | `GET /api/auth/session` |

Dependencies modified this epic: **stripe, @prisma/client, prisma** (schema will grow)

## Dependency Status (Context7 Audit)

> STATUS: Context7 MCP not yet configured. Audit is BLOCKED pending setup.
> Re-run this table once `claude mcp add context7 ...` completes.

| Library                  | Installed       | Current        | Breaking Changes? | Confidence               |
|--------------------------|-----------------|----------------|-------------------|--------------------------|
| next                     | 15.1.0          | {{verify}}     | {{verify}}        | NONE (Context7 unavailable) |
| @prisma/client / prisma  | 5.22.0          | {{verify}}     | {{verify}}        | NONE                     |
| stripe                   | 17.2.0          | {{verify}}     | {{verify}}        | NONE                     |
| ioredis                  | 5.4.1           | {{verify}}     | {{verify}}        | NONE                     |
| zod                      | 3.23.8          | {{verify}}     | {{verify}}        | NONE                     |
| @auth/core               | 0.37.0          | {{verify}}     | {{verify}}        | NONE                     |
| next-auth                | 5.0.0-beta.25   | {{verify}}     | BETA — track upstream | NONE                 |
| typescript               | 5.6.3           | {{verify}}     | {{verify}}        | NONE                     |
| jest / ts-jest           | 29.7.0 / 29.2.5 | {{verify}}     | {{verify}}        | NONE                     |
| eslint                   | 9.14.0          | {{verify}}     | flat config only  | NONE                     |
| tsx                      | 4.19.2          | {{verify}}     | {{verify}}        | NONE                     |

Pre-audit risk flags worth capturing even without Context7:
- **next-auth 5.0.0-beta.25** — beta release; API surface can shift. Pin exactly; re-verify on every resume.
- **Next.js 15 + React 19** — RSC cache semantics and `cookies()`/`headers()` are now async in 15; must be confirmed against Context7 before writing server-side Stripe session code.
- **Stripe 17.x** — webhook signature verification signature + `apiVersion` pinning changed between 14 and 17; Context7 check mandatory before touching webhooks.
- **Prisma 5.22** — last 5.x minor before 6.x; confirm migration path if we later bump.
- **ESLint 9** — flat config only; no legacy `.eslintrc`.

## Environment Variables

{{INVESTIGATE .env.example — expected:}}

| Variable                        | Purpose                       | Required | Where Set        |
|---------------------------------|-------------------------------|----------|------------------|
| DATABASE_URL                    | Postgres connection           | Yes      | .env, CI secrets |
| STRIPE_SECRET_KEY               | Server-side Stripe calls      | Yes      | .env, CI secrets |
| STRIPE_WEBHOOK_SECRET           | Webhook signature verification | Yes     | .env, CI secrets |
| STRIPE_PRICE_* (per plan)       | Plan → Stripe Price mapping   | Yes      | .env, CI secrets |
| NEXTAUTH_SECRET / AUTH_SECRET   | Session token signing         | Yes      | .env, CI secrets |
| NEXTAUTH_URL / AUTH_URL         | Canonical site URL            | Yes      | .env, CI secrets |
| REDIS_URL                       | ioredis connection            | Yes (likely) | .env, CI secrets |
```

**Phase 0 is not complete yet.** What I need from you or from a repo read to finish it:

- `prisma/schema.prisma` (confirm User model and any existing subscription fields)
- `app/api/` tree (existing routes, webhook path, auth route)
- `auth.ts` / `lib/auth.ts` (NextAuth v5 config)
- `.env.example` (env var contract)
- `jest.config.ts` (test layout + any setup file)

Once I've read those, `{{INVESTIGATE}}` markers become real content and the lockfile hash + test baseline get captured.

---

## Phase 1: EPIC.md (draft — needs your confirmation before we go further)

Phase 1 kicks off with **brainstorming**, not drafting. So this is a *strawman* to react to, not a specification. The skill explicitly requires human sign-off on "what/why" before we plan.

```markdown
# Epic: Subscription Management

Last updated: 2026-04-21 — Session 1
Status: DRAFT — awaiting user confirmation of scope

## Business Intent

Enable customers on acme-billing to subscribe to recurring plans, manage their
subscription lifecycle (upgrade, downgrade, cancel, resume), and have billing
state reliably reflected in our database — with Stripe as the source of truth
for billing events and our Postgres as the source of truth for product/feature
entitlement.

## Feasibility Cross-Reference

Against CODEBASE.md dependency map:
- Stripe 17.2.0 supports subscriptions, Checkout, and Billing Portal natively — feasible.
- Prisma 5.22.0 can model Subscription/Plan cleanly — feasible.
- NextAuth v5 beta gives us authed session context server-side — feasible, but beta risk noted.
- ioredis 5.4.1 for webhook idempotency keys — feasible.
- **Context7 verification REQUIRED before PLAN.md** on:
  - `stripe.checkout.sessions.create` signature in 17.x
  - `stripe.webhooks.constructEvent` in 17.x (signature verification)
  - `stripe.billingPortal.sessions.create` in 17.x
  - Next.js 15 App Router RSC + `cookies()`/`headers()` async behavior for auth reads
  - NextAuth v5 `auth()` helper contract in 5.0.0-beta.25

## Human Tasks (identified early)
| Task | Why Agent Can't Do It |
|------|-----------------------|
| Create Stripe account / retrieve test + live API keys | Credentials |
| Create Stripe Products and Prices for each plan (or decide: seeded by us vs. manual in dashboard) | Business decision + credentials |
| Provide STRIPE_WEBHOOK_SECRET from `stripe listen` or dashboard | Credentials |
| Decide cancel behavior: immediate vs. end-of-period | Business decision |
| Decide upgrade/downgrade UX: Stripe Billing Portal (fast) vs. custom UI (branded, more work) | Design judgment |
| Confirm plan catalog + pricing | Business decision |
| Visual QA on checkout redirect flow | Human-only |
```

---

## What I need from you before we proceed

**This is a `--- HANDOFF SAFE ---` boundary.** We're at the end of brainstorming scaffolding and need design sign-off before Phase 2 (PLAN.md).

1. **Answer the six open questions above** (even "I don't know, pick sensible defaults" is a valid answer — just tell me which ones).
2. **Confirm the out-of-scope list** — anything in there you actually want in scope?
3. **Set up Context7** so I can run the dependency audit and the feasibility checks on Stripe 17.x / Next.js 15 / NextAuth v5 before I write the plan. Without it, any plan I produce has LOW confidence on every API call.
4. **Confirm Entire is enabled** — `entire status` should return healthy. This is what makes session 2 able to rewind to the right checkpoint.
5. **Let me read the repo** — specifically `prisma/schema.prisma`, `auth.ts`/`lib/auth.ts`, `app/api/**`, and `.env.example`.

Your move: answer the six questions (or say "pick defaults") and confirm I can read the repo.
