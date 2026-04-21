Got it — let's wrap this up cleanly. Here's the handoff doc for the next session:

---

# HANDOFF.md — Subscription Management

**Branch:** `feature/subscription-mgmt`
**Session:** 2 → 3 handoff
**Date:** 2026-04-21
**Status:** Tasks #1–#6 complete. Ready to proceed to Task #7 (blocked) or Task #8.

---

## TL;DR for the next session

We're 6/8 tasks deep into the Subscription Management epic. All three core subscription lifecycle endpoints (create, upgrade, downgrade) are implemented and passing their unit/integration tests. The remaining work is E2E tests (blocked on Stripe test keys in CI) and a documentation + error-handling pass.

Start here: **Task #8** is the most productive next move since Task #7 is blocked on human action.

---

## What got done this session

- **Task #6 — Downgrade subscription endpoint** is complete.
  - Endpoint wired up, prorated billing logic verified against Stripe's `proration_behavior`.
  - Unit tests + integration tests green.
  - Follows the same pattern as the upgrade endpoint from Task #5 (webhook-driven state sync).

Prior session closed out Tasks #1–#5.

---

## Current state of PLAN.md

```
### Task #1: Database schema for subscriptions [DONE]
### Task #2: Stripe customer sync service [DONE]
### Task #3: Webhook event router [DONE]
### Task #4: Create subscription endpoint [DONE]
### Task #5: Upgrade subscription endpoint [DONE]
### Task #6: Downgrade subscription endpoint [DONE]   <-- just finished

--- HANDOFF SAFE ---

### Task #7: E2E integration tests                    <-- BLOCKED
### Task #8: API docs + error handling audit          <-- recommended next
```

---

## Next steps (in priority order)

### 1. Task #8 — API docs + error handling audit (recommended start)
- Generate OpenAPI spec for all subscription endpoints (create, upgrade, downgrade, and whatever read endpoints exist).
- Standardize error responses across the endpoints — check that they share a common shape (error code, message, details).
- Run a final review on the full diff vs `main` before merge.

### 2. Task #7 — E2E integration tests (when unblocked)
- **Blocker:** needs Stripe test API keys provisioned in CI. This is a human task — ping the user if it's not yet done.
- Once unblocked: full flow test (create → upgrade → downgrade → cancel) against Stripe test mode.

---

## Gotchas / context worth preserving

- **Webhook-driven state model:** subscription state in our DB is authoritative *only after* the Stripe webhook confirms the change. Endpoints kick off the Stripe operation and then rely on the webhook router (Task #3) to reconcile state. Don't add optimistic DB writes.
- **Proration:** upgrade and downgrade both use Stripe's proration. Don't try to compute it ourselves.
- **Task #7 blocker is human-shaped:** no amount of code work will unblock it. If Task #8 finishes and #7 is still blocked, the session should stop and flag it rather than fake credentials.

---

Good stopping point — all tests green, no half-finished work, PLAN.md reflects reality. Commit Task #6 if you haven't already, and the next session can pick up Task #8 cold.
