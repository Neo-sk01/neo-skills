# Plan: Subscription Management
Epic: Subscription Management
Branch: feature/subscription-mgmt
Created: 2026-04-10 — Session 2
Last mutation: none

## Tasks

### Task #1: Database schema for subscriptions [DONE]
- Add Prisma model: Subscription (id, userId, stripeSubscriptionId, stripePriceId, status, currentPeriodEnd, cancelAtPeriodEnd)
- Migration: `npx prisma migrate dev --name add-subscriptions`
- Verified: prisma generate + test seed

### Task #2: Stripe customer sync service [DONE]
- `src/services/stripe-customer.ts` — create/retrieve Stripe customer from user
- Context7 verified: stripe-node customer.create, customer.retrieve (HIGH confidence)

### Task #3: Webhook event router [DONE]
- `src/middleware/stripe-webhook.ts` — signature verification + event dispatch
- Events: customer.subscription.created, updated, deleted, invoice.payment_failed
- Context7 verified: stripe-node webhooks.constructEvent (HIGH confidence)

### Task #4: Create subscription endpoint [DONE]
- `POST /api/subscriptions` — select plan, create Stripe subscription, store locally
- TDD: 8 tests covering plan selection, duplicate prevention, Stripe error handling
- Integration checkpoint: 32 passing, 0 failing

--- HANDOFF SAFE ---

### Task #5: Upgrade subscription endpoint [NEXT]
- `POST /api/subscriptions/:id/upgrade` — change to higher-tier plan with proration
- API contract: `{ targetPriceId: string }` → `{ subscription: Subscription, prorationAmount: number }`
- Stripe API: `stripe.subscriptions.update(id, { items: [{ id: si_xxx, price: newPriceId }], proration_behavior: 'create_prorations' })`
- Context7 check needed: stripe-node subscription.update proration_behavior options
- TDD: RED first — test proration calculation, plan validation, downgrade rejection

### Task #6: Downgrade subscription endpoint
- `POST /api/subscriptions/:id/downgrade` — schedule downgrade at period end
- Decision: use Stripe subscription schedules (see decisions log)
- API contract: `{ targetPriceId: string }` → `{ subscription: Subscription, effectiveDate: string }`
- Context7 check needed: stripe-node subscriptionSchedules.create

### Task #7: E2E integration tests
- Full flow: create → upgrade → downgrade → cancel
- **BLOCKED: needs Stripe test API keys in CI** (human task)
- Test against Stripe test mode

--- HANDOFF SAFE ---

### Task #8: API documentation + error handling audit
- OpenAPI spec generation for all subscription endpoints
- Error response standardization
- Final Codex review on full diff vs main
