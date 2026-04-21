Got it — I've read both files. Here's where things stand and what I'd suggest we do next.

## Status recap

**Session 4 ended clean** with tasks #1–#4 done (schema, Stripe customer sync, webhook router, create endpoint). 32 tests passing, Codex review clean, working tree clean on `feature/subscription-mgmt` at `.worktrees/subscription-mgmt`.

**Next action per your handoff:** Task #5 — `POST /api/subscriptions/:id/upgrade` with proration, starting with a TDD RED.

**Key context to carry forward:**
- Stripe API version migration analysis is archived in checkpoint `ck_7f8e9a2b` (session 3)
- Pricing tier edge cases are in `ck_3d4e5f6a` — only load if proration logic gets tricky
- Decision log: downgrades use Stripe subscription schedules (relevant for task #6, not #5)
- Open human task: Stripe test API keys in CI (blocks #7, not us yet)

## One thing I can't actually do

You asked me to run `entire status` — I don't have shell access to your environment in this turn, so I can't execute it. **Could you run it and paste the output?** Specifically I want to confirm:

1. `entire status` → healthy
2. Latest checkpoint is still `ck_5e6f7a8b` (matches handoff)
3. No shadow branch drift since session 4 closed

If it comes back healthy, I'll proceed without pulling any archaeology — the handoff says proceed directly to task #5 unless Stripe's update API behaves unexpectedly.

## Proposed plan for task #5

Following the contract in PLAN.md:

**Contract**
- Route: `POST /api/subscriptions/:id/upgrade`
- Request: `{ targetPriceId: string }`
- Response: `{ subscription: Subscription, prorationAmount: number }`
- Stripe call: `stripe.subscriptions.update(id, { items: [{ id: si_xxx, price: newPriceId }], proration_behavior: 'create_prorations' })`

**Before I touch code, one Context7 check I want to run** (PLAN.md flags it as needed):
- `stripe-node` → `subscription.update` proration_behavior options, and how to retrieve the upcoming invoice preview for returning `prorationAmount`. The plan says use `create_prorations`, but I want to confirm whether we should also call `stripe.invoices.retrieveUpcoming` to compute `prorationAmount` accurately, or whether the update response carries it. Installed version is 17.2.0 per the doc verifications table.

**TDD RED tests I'll write first** (in `src/api/subscriptions/[id]/upgrade.test.ts` or wherever your pattern puts them — I'll match the layout from `subscription.create.test.ts`):

1. Rejects when `targetPriceId` is missing or not a string (400)
2. Rejects when subscription ID doesn't exist / doesn't belong to the authenticated user (404)
3. Rejects when `targetPriceId` is a lower-tier plan than current (400 — "use downgrade endpoint") — this is the "downgrade rejection" the plan calls out
4. Rejects when `targetPriceId` equals current price (400 — no-op)
5. Happy path: calls Stripe with correct `items[].id` (existing subscription item) and `price` (new), `proration_behavior: 'create_prorations'`
6. Happy path: persists updated `stripePriceId` and `currentPeriodEnd` locally
7. Happy path: returns `{ subscription, prorationAmount }` with proration from Stripe preview
8. Stripe error path: propagates Stripe `StripeInvalidRequestError` as 400, `StripeAPIError` as 502

**Then GREEN** in `src/services/subscription.ts` — add `upgradeSubscription(subscriptionId, userId, targetPriceId)` — and wire the route handler.

**Then integration checkpoint** (expect 32 → ~40 passing).

## Open questions before I start

1. **Entire status output?** (blocker above)
2. **Plan tier ordering** — how is "higher tier" determined? Is there a `tier` or `rank` column on the plan/price config, or do I need to hit Stripe product metadata? I didn't see this in the handoff or plan; might be in `CODEBASE.md`. If you can point me at where price tiers are defined, I can encode the downgrade-rejection logic correctly. Otherwise I'll default to looking it up from a local plans config and flag it if missing.
3. **prorationAmount semantics** — should this be the proration credit/charge in cents (Stripe-native) or dollars? I'll default to cents to match Stripe unless you say otherwise.

Once you confirm `entire status` and answer #2, I'll run the Context7 check on `stripe.subscriptions.update` + invoice preview, then write the RED tests.
