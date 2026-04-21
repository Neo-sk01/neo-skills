# Response: Standard Claude Code Agent — Mid-Session Resume

## Approach (No Skill)

I can see from your handoff document that you're working on a subscription management feature and you're at task #5 — implementing the upgrade endpoint.

### Understanding the Context

From the handoff:
- You've completed tasks 3 and 4 (subscription model + downgrade endpoint)
- Next up is the upgrade endpoint: `PUT /api/subscriptions/[id]/upgrade`
- The downgrade is already done, so we can mirror that pattern
- Stripe proration was verified — need `proration_behavior: 'always_invoice'`

### Let me continue with Task #5

I'll implement the upgrade endpoint. Based on what's described, here's the approach:

```typescript
// app/api/subscriptions/[id]/upgrade/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function PUT(
  req: NextRequest,
  { params }: { params: { id: string } }
) {
  const { newPriceId } = await req.json();
  
  const subscription = await prisma.subscription.findUnique({
    where: { id: params.id },
    include: { plan: true }
  });

  if (!subscription) {
    return NextResponse.json({ error: 'Subscription not found' }, { status: 404 });
  }

  // Get the Stripe subscription to find the item ID
  const stripeSubscription = await stripe.subscriptions.retrieve(
    subscription.stripeSubscriptionId
  );

  // Update with proration
  const updated = await stripe.subscriptions.update(
    subscription.stripeSubscriptionId,
    {
      items: [{
        id: stripeSubscription.items.data[0].id,
        price: newPriceId,
      }],
      proration_behavior: 'always_invoice',
    }
  );

  // Update local database
  const newPlan = await prisma.plan.findUnique({
    where: { stripePriceId: newPriceId }
  });

  await prisma.subscription.update({
    where: { id: params.id },
    data: {
      planId: newPlan!.id,
      currentPeriodEnd: new Date(updated.current_period_end * 1000),
    }
  });

  return NextResponse.json({ subscription: updated });
}
```

### Next Steps

After this, I'd:
1. Write tests for the upgrade endpoint
2. Move on to task #6 — the webhook handler for `subscription.updated` events

Want me to proceed with the implementation and tests?
