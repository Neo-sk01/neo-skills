Resuming session 5 on acme-billing, Subscription Management epic. Following the Compact Reload Packet from HANDOFF.md literally.

---

## Resume Protocol Applied

**Step 1 — Handoff loaded.** Session 4, clean handoff, 4 of 8 tasks complete, no plan mutations, working tree clean at commit `a1b2c3d`.

**Step 2 — `entire status`:** healthy (per session 4's `entire status --detailed` review, shadow branch clean, `entire doctor` not needed). Assuming no drift since session 4 closed cleanly; I'll run it now before touching code.

```bash
entire status
```

Expected: healthy, on `feature/subscription-mgmt`, latest checkpoint `ck_5e6f7a8b` at `a1b2c3d`.

**Step 3 — Required reload order (from packet):**
1. ✅ `HANDOFF.md` — loaded
2. ✅ `PLAN.md` — loaded; task #5 confirmed as next
3. ⏭ `src/services/subscription.ts` — will open when entering TDD GREEN

**Step 4 — Entire archaeology (following escalation rule):** The packet says *"proceed directly from handoff for task #5 implementation; inspect Entire only if the Stripe subscription update API behaves unexpectedly or if pricing tier logic is unclear."* So I will **not** run the three archaeology targets yet. They're parked as standby:

- `entire explain --commit a1b2c3d --short` — webhook sig verification reasoning (task #4 context)
- `entire explain --checkpoint ck_7f8e9a2b --full` — Stripe API version migration analysis (session 3)
- `entire explain --checkpoint ck_3d4e5f6a --raw-transcript` — pricing edge cases, only if proration gets tricky

**Avoiding:** session 1-2 transcripts (architecture already in CODEBASE.md).

**Budget target:** ~4200 reload + ~1800 archaeology reserve = 6000 tokens, per session 4's target.

---

## Pipeline Position — Session 5 Start

- Superpowers stage: **executing**
- Active skill: **test-driven-development** (entering RED for task #5)
- Current task: **#5 — `POST /api/subscriptions/:id/upgrade` with proration**
- Worktree: `feature/subscription-mgmt` at `.worktrees/subscription-mgmt`
- Latest checkpoint: `ck_5e6f7a8b` at commit `a1b2c3d`

## Unblocked Items Check

- Human task (Stripe test API keys in CI) — **still pending**. It blocks task #7 (E2E), not task #5. Continue.
- Codex job `codex_r4a` — completed session 4, no issues. Nothing to retrieve.
- LOW/NONE doc-checks — none outstanding. All session 4 Context7 checks were HIGH.

---

## Task #5 — Upgrade Endpoint (TDD RED)

From PLAN.md:
- Route: `POST /api/subscriptions/:id/upgrade`
- Contract: `{ targetPriceId: string }` → `{ subscription: Subscription, prorationAmount: number }`
- Stripe call: `stripe.subscriptions.update(id, { items: [{ id: si_xxx, price: newPriceId }], proration_behavior: 'create_prorations' })`
- Context7 check required: `stripe-node subscription.update proration_behavior options`

### Step 1 — Context7 doc-check first (per skill rule: Context7 at TDD RED)

Before writing the test, verify `proration_behavior` options for stripe-node 17.2.0:

```
resolve-library-id("stripe-node")          → ctx7_stripe_node (cached from session 4)
query-docs(ctx7_stripe_node,
           "subscriptions.update proration_behavior options 17.2.0")
```

Expected finding: `proration_behavior` accepts `'create_prorations' | 'none' | 'always_invoice'`. `create_prorations` generates proration line items on the next invoice without immediately invoicing — which is what the plan calls for. Confidence target: **HIGH**. I'll log this in the session 5 handoff Doc Verifications table.

### Step 2 — Write the failing test (RED)

Following the conventions seeded by session 4's `subscription.create.test.ts`:

```ts
// src/services/__tests__/subscription.upgrade.test.ts
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { upgradeSubscription } from '../subscription';
import { stripe } from '../../lib/stripe';
import { prisma } from '../../lib/prisma';

vi.mock('../../lib/stripe');
vi.mock('../../lib/prisma');

describe('upgradeSubscription', () => {
  const subscriptionId = 'sub_local_123';
  const stripeSubscriptionId = 'sub_stripe_abc';
  const currentItemId = 'si_current_xyz';
  const currentPriceId = 'price_tier_starter';
  const targetPriceId = 'price_tier_pro';

  beforeEach(() => {
    vi.resetAllMocks();
    (prisma.subscription.findUnique as any).mockResolvedValue({
      id: subscriptionId,
      stripeSubscriptionId,
      stripePriceId: currentPriceId,
      status: 'active',
    });
  });

  it('updates the Stripe subscription with create_prorations behavior', async () => {
    (stripe.subscriptions.retrieve as any).mockResolvedValue({
      id: stripeSubscriptionId,
      items: { data: [{ id: currentItemId, price: { id: currentPriceId } }] },
    });
    (stripe.subscriptions.update as any).mockResolvedValue({
      id: stripeSubscriptionId,
      items: { data: [{ id: currentItemId, price: { id: targetPriceId } }] },
      latest_invoice: { amount_due: 1200 },
    });
    (prisma.subscription.update as any).mockResolvedValue({
      id: subscriptionId,
      stripeSubscriptionId,
      stripePriceId: targetPriceId,
      status: 'active',
    });

    const result = await upgradeSubscription(subscriptionId, { targetPriceId });

    expect(stripe.subscriptions.update).toHaveBeenCalledWith(
      stripeSubscriptionId,
      {
        items: [{ id: currentItemId, price: targetPriceId }],
        proration_behavior: 'create_prorations',
      },
    );
    expect(result.subscription.stripePriceId).toBe(targetPriceId);
    expect(typeof result.prorationAmount).toBe('number');
  });

  it('rejects downgrade attempts (target tier rank <= current)', async () => {
    await expect(
      upgradeSubscription(subscriptionId, { targetPriceId: currentPriceId }),
    ).rejects.toThrow(/not an upgrade/i);
    expect(stripe.subscriptions.update).not.toHaveBeenCalled();
  });

  it('rejects unknown target price IDs', async () => {
    await expect(
      upgradeSubscription(subscriptionId, { targetPriceId: 'price_bogus' }),
    ).rejects.toThrow(/unknown plan/i);
  });

  it('surfaces Stripe errors without partial local writes', async () => {
    (stripe.subscriptions.retrieve as any).mockResolvedValue({
      id: stripeSubscriptionId,
      items: { data: [{ id: currentItemId, price: { id: currentPriceId } }] },
    });
    (stripe.subscriptions.update as any).mockRejectedValue(
      new Error('stripe_api_error'),
    );

    await expect(
      upgradeSubscription(subscriptionId, { targetPriceId }),
    ).rejects.toThrow('stripe_api_error');
    expect(prisma.subscription.update).not.toHaveBeenCalled();
  });
});
```

Running `npm test` here should show **4 new failures** (function not yet implemented). That's our RED.

### Step 3 — GREEN implementation sketch (next after RED confirmed)

[... implementation sketch with `proration_behavior: 'create_prorations'` per fixture contract ...]

Flagging one edge case for your awareness, not a blocker: the `PLAN_TIER_RANK` map above assumes three tiers. If your real pricing catalog is dynamic (pulled from Stripe), I should source the rank from a config/DB lookup instead of a hardcoded record — quick decision I'll queue for you if it comes up during GREEN. If it gets murky, the escalation rule says to crack open `ck_3d4e5f6a --raw-transcript` for the session-3 pricing edge case analysis.

Starting with `entire status` and the Context7 check now.
