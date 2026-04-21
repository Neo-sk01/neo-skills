# Plan: Subscription Management
Epic: Subscription Management
Branch: feature/subscription-mgmt
Created: 2026-04-10 — Session 2
Last mutation: none

## Tasks

### Task #1: Database schema for subscriptions [DONE]
### Task #2: Stripe customer sync service [DONE]
### Task #3: Webhook event router [DONE]
### Task #4: Create subscription endpoint [DONE]
### Task #5: Upgrade subscription endpoint [DONE]
### Task #6: Downgrade subscription endpoint [DONE]

--- HANDOFF SAFE ---

### Task #7: E2E integration tests
- Full flow: create → upgrade → downgrade → cancel
- **BLOCKED: needs Stripe test API keys in CI** (human task)
- Test against Stripe test mode

### Task #8: API documentation + error handling audit
- OpenAPI spec generation for all subscription endpoints
- Error response standardization
- Final Codex review on full diff vs main
