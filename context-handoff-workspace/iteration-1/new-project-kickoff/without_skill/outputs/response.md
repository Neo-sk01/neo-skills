Nice — thanks for sharing the package.json. That's a pretty clean, modern stack. Here's what I'm seeing and how I'd approach starting subscription management:

## Stack observations

- **Next.js 15.1** with the App Router (assumed based on version)
- **Prisma 5.22** for the ORM
- **Stripe 17.2** — a recent SDK, which matters because Stripe's API versions pin to the SDK default
- **NextAuth v5 (beta)** — important, because v5's API differs significantly from v4; session handling and the `auth()` helper are the new patterns
- **ioredis** — you've already got Redis wired up, useful for webhook idempotency keys and rate limiting
- **Zod** — good, we should validate webhook payloads and API inputs with it

One flag: `next-auth@5.0.0-beta.25` is still beta. Totally usable, just keep an eye on breaking changes between betas if you ever upgrade.

## Before I dump a plan on you — a few questions

To give you a useful starting point rather than a generic tutorial, it'd help to know:

1. **What subscription model?** Single tier, multiple tiers (e.g., Free / Pro / Enterprise), usage-based metering, seat-based for teams, or something else?
2. **Billing cadence?** Monthly only, monthly + annual, custom trials?
3. **Who owns a subscription?** A `User`, an `Organization`/`Team`, or both?
4. **Does `schema.prisma` already have a `User` model tied to NextAuth?** If so, we extend it. If not, we set that up first.
5. **Is Stripe already set up** (products/prices created in the dashboard or via code), or are we starting from zero there too?

## If you want to start moving now, here's the typical shape

Even without those answers, the skeleton is almost always:

### 1. Prisma schema additions

You'll want something like:

```prisma
model User {
  // ...existing NextAuth fields
  stripeCustomerId   String?       @unique
  subscription       Subscription?
}

model Subscription {
  id                   String   @id @default(cuid())
  userId               String   @unique
  user                 User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  stripeSubscriptionId String   @unique
  stripePriceId        String
  stripeCustomerId     String
  status               String   // active, trialing, past_due, canceled, etc.
  currentPeriodEnd     DateTime
  cancelAtPeriodEnd    Boolean  @default(false)
  createdAt            DateTime @default(now())
  updatedAt            DateTime @updatedAt

  @@index([stripeCustomerId])
}
```

Then `npm run db:migrate` (you've got the script wired up already).

### 2. Stripe client singleton

`lib/stripe.ts`:

```ts
import Stripe from 'stripe'

export const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2024-10-28.acacia', // or whatever your account is pinned to
  typescript: true,
})
```

### 3. Three route handlers (App Router)

- `app/api/stripe/checkout/route.ts` — creates a Checkout Session, passing the authenticated user's `stripeCustomerId` (create one lazily if missing)
- `app/api/stripe/portal/route.ts` — returns a Billing Portal URL so users can manage their own sub
- `app/api/stripe/webhook/route.ts` — handles `checkout.session.completed`, `customer.subscription.updated`, `customer.subscription.deleted`, `invoice.payment_failed`

### 4. Webhook details that bite people

A few things to get right the first time:

- Use `export const runtime = 'nodejs'` on the webhook route (not edge) — you need the raw body
- Read with `await req.text()` and pass to `stripe.webhooks.constructEvent(rawBody, sig, webhookSecret)` — do NOT parse JSON first
- Use `ioredis` to dedupe by `event.id` so retried events don't double-apply
- Wrap DB writes in a transaction and upsert on `stripeSubscriptionId`

### 5. NextAuth v5 integration

In v5, you'd read the session in route handlers via:

```ts
import { auth } from '@/auth'
const session = await auth()
if (!session?.user) return new Response('Unauthorized', { status: 401 })
```

## What I'd suggest for this session

If you're up for it, let's do it in this order:

1. You answer the 5 questions above (or as many as you know)
2. I'll look at your existing `schema.prisma` and `auth.ts`/`auth.config.ts` so I can write migrations and route handlers that actually fit your codebase
3. We implement Prisma changes + Stripe client + checkout route, get one happy path working end-to-end
4. Then webhooks (which is where the complexity actually lives)
5. Then the portal + UI bits

Want to kick off with question 1, or would you prefer I peek at the repo's existing Prisma schema and auth setup first? If you paste `schema.prisma` and `auth.ts` (or wherever your NextAuth config lives), I can be much more concrete.
