# Response: With Context-Handoff Skill — New Project Kickoff

## Phase 0: Codebase Understanding (First Session)

The skill prescribes starting with Phase 0 before any implementation work. Here's the structured approach:

### Step 1: Map the Architecture

```bash
# Explore project structure
ls -la
cat package.json
cat prisma/schema.prisma
ls src/ or app/
```

Building a mental model of:
- Entry points (Next.js App Router structure)
- Service boundaries (API routes vs server components vs client)
- Data flow (Prisma → DB, Stripe SDK → Stripe API)
- Key abstractions and their relationships

### Step 2: Context7 Dependency Audit

Run doc verification on all major dependencies with confidence scoring:

```
Context7: resolve-library-id("next.js", "app router API routes")
Context7: query-docs(<next-id>, "Next.js 14 API route handlers with dynamic segments")
→ Confidence: HIGH

Context7: resolve-library-id("prisma", "schema models relations")
Context7: query-docs(<prisma-id>, "Prisma 5 schema definition with relations and enums")
→ Confidence: HIGH

Context7: resolve-library-id("stripe", "subscriptions API")
Context7: query-docs(<stripe-id>, "Stripe subscription create update cancel with proration Node.js SDK 14")
→ Confidence: HIGH
```

### Step 3: Capture Baseline

```bash
# Lockfile hash for dependency tracking
sha256sum package-lock.json  # or yarn.lock / pnpm-lock.yaml

# Test baseline
npm test  # capture current passing/failing counts

# Build check
npm run build
```

### Step 4: Generate CODEBASE.md

Following the CODEBASE-TEMPLATE.md:

```markdown
# Codebase: subscription-manager
Last updated: 2026-04-12 — Session 1

## Architecture
Next.js 14 App Router application with Prisma ORM for PostgreSQL and Stripe
integration for payments. The app uses server components by default with client
components for interactive UI. API routes handle REST endpoints under app/api/.
Authentication is handled via NextAuth.js with JWT sessions.

## File Structure
```
app/
├── api/              # REST API route handlers
│   ├── auth/         # NextAuth endpoints
│   └── webhooks/     # External service webhooks
├── (dashboard)/      # Dashboard route group
├── layout.tsx        # Root layout with providers
└── page.tsx          # Landing page
prisma/
├── schema.prisma     # Database schema
├── migrations/       # Migration history
└── seed.ts           # Seed data
lib/
├── prisma.ts         # Prisma client singleton
├── stripe.ts         # Stripe client singleton
└── auth.ts           # Auth configuration
```

## Conventions
- **Naming:** camelCase for variables/functions, PascalCase for components/types
- **File organization:** One route handler per file, collocated with route
- **Error handling:** NextResponse.json with appropriate status codes
- **Logging:** console.log in development, structured logging TBD
- **Import ordering:** external → internal → relative

## Test Patterns
- **Unit tests:** Vitest, colocated __tests__ directories
- **Mocking strategy:** Stripe SDK mocked for unit tests, real for integration

## Build & Deploy
- **Build:** `npm run build`
- **Test:** `npm test`
- **Lint:** `npm run lint`

## Cross-System Dependency Map
| System | Type | Interface | Contract | Health Check |
|---|---|---|---|---|
| PostgreSQL | Database | Prisma ORM | schema.prisma | `npx prisma db pull` |
| Stripe | Payment API | stripe-node SDK v14 | API version 2024-04 | Test mode ping |
| NextAuth | Auth | OIDC/JWT | Session callbacks | `/api/auth/session` |

Dependencies modified this epic: Stripe (new subscription endpoints), Prisma (new models)

## Dependency Status (Context7 Audit)
| Library | Installed | Current | Breaking Changes? | Confidence |
|---|---|---|---|---|
| next | 14.2.0 | 15.1.0 | Yes — App Router changes | HIGH |
| prisma | 5.22.0 | 5.22.0 | No | HIGH |
| stripe | 14.0.0 | 17.2.0 | Yes — webhook sig change | MEDIUM |
| next-auth | 4.24.0 | 5.0.0 | Yes — v5 rewrite | HIGH |

## Domain Knowledge
- Stripe test mode requires separate API keys from production
- Webhook endpoints must verify signatures to prevent spoofing
- Subscription proration behavior varies by upgrade vs downgrade

## Environment Variables
| Variable | Purpose | Required | Where Set |
|---|---|---|---|
| DATABASE_URL | PostgreSQL connection | Yes | .env, CI |
| STRIPE_SECRET_KEY | Stripe API access | Yes | .env, CI |
| STRIPE_WEBHOOK_SECRET | Webhook signature verification | Yes | .env, CI |
| NEXTAUTH_SECRET | Session encryption | Yes | .env, CI |
| NEXTAUTH_URL | Auth callback base URL | Yes | .env |
```

---

## Phase 1: Epic Framing

### Brainstorming Activation

Cross-referencing design against CODEBASE.md dependency map:
- **Feasibility:** Stripe SDK v14 supports subscriptions API ✅ (Context7 HIGH)
- **Feasibility:** Prisma supports the schema changes needed ✅ (Context7 HIGH)
- **Risk:** Stripe SDK v14 → v17 has breaking changes (Context7 MEDIUM) — stay on v14 for now
- **FAILURES.md check:** No prior failures (first session)

### Human Tasks Identified Early

| Task | Why Agent Can't Do It | Priority |
|------|----------------------|----------|
| Stripe API keys (live mode) | Requires account access | Before deploy |
| Pricing decisions (plan tiers, amounts) | Business decision | Before UI |
| Visual QA of pricing page | Design judgment | After frontend |

### Generate EPIC.md

```markdown
# Epic: Subscription Management

## Business Intent
Add subscription management to allow users to subscribe to plans, upgrade/downgrade
between tiers, and cancel. Integrates with Stripe for payment processing.

## Scope
**In scope:**
- Prisma models for plans and subscriptions
- CRUD API endpoints for subscription lifecycle
- Stripe checkout, upgrade, downgrade, cancel flows
- Webhook handler for Stripe events
- Admin dashboard for subscription management
- API documentation

**Out of scope:**
- Usage-based billing
- Multi-currency support
- Team/org subscriptions
- Custom enterprise pricing

## Human Tasks
- Stripe API keys for production
- Plan pricing and tier definitions
- Visual QA on pricing page

## Success Criteria
- Users can subscribe, upgrade, downgrade, cancel
- All Stripe events handled idempotently
- Full test coverage on all endpoints
- Admin can view and manage subscriptions
```

---

## Phase 2: Plan Design (Preview)

At this point the skill would activate the worktree + writing-plans workflow:

```bash
# Create isolated worktree
git worktree add ./worktrees/feat-subscriptions -b feat/subscriptions
```

Then generate PLAN.md with:
- 8-10 TDD-backed tasks
- `--- HANDOFF SAFE ---` boundaries between tasks
- Integration checkpoint markers after batches
- Context7 verification on all library methods
- Human task dependencies marked

### Plan Preview (Tasks 1-8)

1. Create Plan and Subscription Prisma models + migration
2. Seed plan data (basic, pro, enterprise tiers)
3. Implement POST /api/subscriptions/checkout (Stripe Checkout Session)
4. Implement PUT /api/subscriptions/[id]/downgrade
5. Implement PUT /api/subscriptions/[id]/upgrade
6. Implement POST /api/webhooks/stripe (event handler)
7. Build admin subscription dashboard
8. Write API documentation + deployment guide

Each task follows TDD: RED (write failing test) → GREEN (implement) → REFACTOR → commit.

Integration checkpoints after tasks 3, 6, and 8.

---

## Session Handoff Readiness

Since this is Phase 0 + Phase 1 (mapping + framing), this is a ✅ CLEAN handoff point per the Handoff Timing Map. The skill would now generate a HANDOFF.md with:
- Compact reload packet pointing to CODEBASE.md, EPIC.md, PLAN.md
- Context budget data
- Entire checkpoint references
- Doc verification table (all Context7 checks logged)
- Human tasks queue

**Next session starts at Phase 2 execution — task #1 of the plan.**
