# Response: Standard Claude Code Agent — New Project Kickoff

## Approach (No Skill)

Here's how I'd help you get started with the subscription management feature:

### 1. Explore the Codebase

First, I'd look at the project structure to understand what we're working with:
- Check `package.json` for dependencies and scripts
- Look at the Prisma schema for existing models
- Review the API routes structure
- Check for existing Stripe integration

### 2. Design the Prisma Schema

```prisma
model Plan {
  id          String   @id @default(cuid())
  name        String
  stripePriceId String @unique
  price       Int
  interval    String   // "month" or "year"
  features    Json
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  subscriptions Subscription[]
}

model Subscription {
  id                String   @id @default(cuid())
  userId            String
  planId            String
  stripeSubscriptionId String @unique
  stripeCustomerId  String
  status            String   // active, canceled, past_due
  currentPeriodEnd  DateTime
  cancelAtPeriodEnd Boolean  @default(false)
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
  user              User     @relation(fields: [userId], references: [id])
  plan              Plan     @relation(fields: [planId], references: [id])
}
```

### 3. Implement API Routes

I'd create these API routes:

- `POST /api/subscriptions/checkout` — Create Stripe checkout session
- `PUT /api/subscriptions/[id]/upgrade` — Upgrade plan with proration
- `PUT /api/subscriptions/[id]/downgrade` — Downgrade plan
- `DELETE /api/subscriptions/[id]` — Cancel subscription
- `POST /api/webhooks/stripe` — Handle Stripe webhook events

### 4. Stripe Webhook Handler

Handle these events:
- `checkout.session.completed` — Activate subscription
- `invoice.paid` — Renew subscription
- `invoice.payment_failed` — Mark as past_due
- `customer.subscription.updated` — Sync plan changes
- `customer.subscription.deleted` — Mark as canceled

### 5. Frontend Pages

- `/pricing` — Display plans with upgrade/downgrade CTAs
- `/billing` — Current subscription, usage, invoices
- `/billing/success` — Post-checkout confirmation

### 6. Implementation Order

1. Run Prisma migration for new models
2. Set up Stripe SDK and environment variables
3. Build checkout flow (creates subscription)
4. Build upgrade/downgrade endpoints
5. Build webhook handler
6. Build cancel flow
7. Build frontend pages
8. Add error handling and edge cases

### 7. Edge Cases to Handle

- Proration on plan changes (Stripe handles this, but we need to configure `proration_behavior`)
- Failed payments and retry logic
- Webhook idempotency (store event IDs to prevent double-processing)
- Race conditions between webhook and redirect

### 8. Local Testing

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```

Use Stripe test mode with test card numbers.

---

**Shall I start with the Prisma schema migration?**
