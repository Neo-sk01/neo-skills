# Response: With Context-Handoff Skill — Mid-Session Resume

## Resume Protocol Execution

### Step 1: Read HANDOFF.md (Already Provided)

Handoff loaded. Key extraction:
- **Type:** clean
- **Session:** 3 → This is session 4
- **Epic:** Subscription Management
- **Plan progress:** 4 of 8 tasks complete
- **Exact next action:** Implement PUT /api/subscriptions/[id]/upgrade endpoint
- **Pipeline position:** Executing stage, TDD skill active

### Step 2: Run `entire status`

```bash
entire status
```

Checking repository and session health before proceeding. Looking for:
- Healthy checkpoint state
- No stale shadow branches
- Clean working tree (confirmed by handoff: "None — working tree clean")

### Step 3: Load Files in Prescribed Reload Order

Following the Compact Reload Packet's required reload order:

1. **HANDOFF.md** — ✅ Already loaded (current state + verification evidence)
2. **PLAN.md** — Loading to review task #5 specification and dependencies
3. **CODEBASE.md** — Loading for Stripe integration patterns and conventions

### Step 4: Entire Archaeology (Targeted)

Following the handoff's archaeology targets:

```bash
# First: compact reasoning about schema decisions
entire explain --commit abc1234 --short
```

This recovers the reasoning behind subscription model schema choices made in session 2-3. Need this context before building the upgrade endpoint on top of those models.

**Archaeology escalation rule check:** The handoff says "Proceed directly from handoff for task 5; inspect Entire only if Stripe API behavior differs from session 2 findings." Since the Context7 doc verification shows HIGH confidence on Stripe proration API, I can proceed directly without deeper archaeology.

**Skipping:** `entire explain --checkpoint def5678 --full` — The handoff's escalation rule says this is only needed if Stripe API behavior differs. Context7 verification at HIGH confidence, so proceeding without.

### Step 5: Context Budget Assessment

From the handoff:
- Resume budget target: ~3000 tokens for docs + ~1500 for Entire archaeology
- Average tokens per task: ~12000
- Reserved buffer: 8000
- 4 tasks remaining (5-8)

Estimate: ~48,000 tokens needed for remaining tasks + overhead. This session should comfortably handle 2-3 tasks.

### Step 6: Leveraging Prior Doc Verifications

From the handoff's Context7 table:
- **Stripe subscription update with proration** → `proration_behavior` param required — **HIGH confidence** ✅
- **Prisma transaction with nested writes** → Supported via `$transaction` — **HIGH confidence** ✅

Both are HIGH confidence, so I can trust these for task #5 without re-verification.

### Step 7: Continue with Task #5 — Subscription Upgrade Endpoint

**Activating TDD workflow (RED → GREEN → REFACTOR):**

#### RED Phase — Write the failing test first

Before writing the test, run Context7 doc-check on the specific API:

```
Context7: resolve-library-id("stripe", "subscription update proration")
Context7: query-docs(<stripe-id>, "stripe subscriptions update proration_behavior always_invoice")
```

Test outline:
```typescript
describe('PUT /api/subscriptions/[id]/upgrade', () => {
  it('should upgrade subscription with proration_behavior always_invoice', async () => {
    // Arrange: existing subscription on basic plan
    // Act: PUT request with premium plan priceId
    // Assert: Stripe called with proration_behavior: 'always_invoice'
    // Assert: local DB updated with new plan
    // Assert: returns updated subscription
  });

  it('should return 404 for non-existent subscription', async () => {
    // ...
  });

  it('should return 400 when upgrading to same plan', async () => {
    // ...
  });

  it('should handle Stripe API errors gracefully', async () => {
    // ...
  });
});
```

#### GREEN Phase — Implement the endpoint

Mirror the downgrade pattern (from task #4, commit abc1234) but with key difference:
- Use `proration_behavior: 'always_invoice'` (vs downgrade's `create_prorations`)

```typescript
// app/api/subscriptions/[id]/upgrade/route.ts
export async function PUT(req, { params }) {
  const { newPriceId } = await req.json();
  
  const subscription = await prisma.subscription.findUnique({
    where: { id: params.id },
    include: { plan: true }
  });

  if (!subscription) return NextResponse.json({ error: 'Not found' }, { status: 404 });

  const stripeSubscription = await stripe.subscriptions.retrieve(
    subscription.stripeSubscriptionId
  );

  const updated = await stripe.subscriptions.update(
    subscription.stripeSubscriptionId,
    {
      items: [{
        id: stripeSubscription.items.data[0].id,
        price: newPriceId,
      }],
      proration_behavior: 'always_invoice', // Key difference from downgrade
    }
  );

  // Update local DB in transaction
  const newPlan = await prisma.plan.findUniqueOrThrow({
    where: { stripePriceId: newPriceId }
  });

  await prisma.$transaction([
    prisma.subscription.update({
      where: { id: params.id },
      data: {
        planId: newPlan.id,
        currentPeriodEnd: new Date(updated.current_period_end * 1000),
      }
    })
  ]);

  return NextResponse.json({ subscription: updated });
}
```

#### REFACTOR Phase — Clean up, then commit

After tests pass:
- Check for code duplication with downgrade endpoint
- Consider extracting shared Stripe subscription update logic
- Commit with descriptive message

```bash
git commit -m "feat: add subscription upgrade endpoint with proration

Mirrors downgrade pattern but uses proration_behavior: 'always_invoice'
per Stripe docs (Context7 HIGH confidence).

Entire-Checkpoint: [auto-generated]
Entire-Attribution: [auto-generated]"
```

### Monitoring

- **Context budget:** Tracking tokens consumed against remaining tasks
- **Rollback readiness:** If upgrade endpoint fails 3 times, will rollback to this commit
- **Integration checkpoint:** After task #5 + #6, run full test suite
- **Codex review:** Not needed for this task (mirrors existing pattern, good test coverage)
- **Next handoff-safe boundary:** After this commit (between tasks)
