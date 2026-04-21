# ⚠ INVALIDATED — Pre-Grounding Run

This directory contains eval runs generated against an **earlier version of `evals/evals.json`** that had empty `files: []` for every eval. The outputs and gradings here were valid against those prose-only assertions but are **stale relative to the current fixture-grounded eval spec**.

## Why Invalidated

The current `evals/evals.json` (post-Codex-review-round-1) now requires grounded evidence from fixtures in `evals/fixtures/`:

| Eval | Fixture(s) | Ground truth |
|------|-----------|---------------|
| new-project-kickoff | `package-json-acme.json` | next@15.1.0, stripe@17.2.0, ioredis@5.4.1, next-auth@5.0.0-beta.25 |
| mid-session-resume | `handoff-session-4.md`, `plan-subscription-mgmt.md` | Reload order HANDOFF.md → PLAN.md → src/services/subscription.ts; checkpoints ck_7f8e9a2b, ck_5e6f7a8b, commit a1b2c3d; task #5 is `POST /api/subscriptions/:id/upgrade` with `create_prorations` |
| budget-pressure-handoff | `plan-with-task6-done.md` | 6 of 8 tasks done; next action is task #7 (E2E tests, blocked on Stripe test API keys in CI) or task #8 (API docs) |

The outputs in this directory contradict those ground truths:

- **new-project-kickoff** hallucinates next@14.2.0, stripe@14.0.0, next-auth@4.24.0 and omits Redis despite `ioredis` being in the fixture package.json
- **mid-session-resume** loads `CODEBASE.md` first (the fixture says HANDOFF.md → PLAN.md → src/services/subscription.ts), invents checkpoint IDs `abc1234`/`def5678`, and changes the API contract to `PUT` with `always_invoice` instead of `POST` with `create_prorations`
- **budget-pressure-handoff** routes the next session to an "admin dashboard" and queues unrelated human tasks, completely missing the fixture's blocker (Stripe test API keys in CI blocking task #7)

A handoff that misstates the next action is worse than no handoff — it will send the next session down the wrong path. Publishing these as a passing benchmark would certify exactly the failure mode this skill is supposed to prevent.

## What's Valid Here

- `*/eval_metadata.json` — still describes the eval shape
- `*/timing.json` — token/duration data from the actual agent runs is real
- The *baseline* (`without_skill`) outputs are also real — baseline has no fixture expectations, so it's neutral

## What's Needed

A fresh iteration-1 run against the grounded eval spec:

1. Spawn 6 agents (3 evals × 2 configurations) with the fixture files loaded as inputs
2. Grade each against the new assertions in `evals/evals.json`
3. Regenerate `benchmark.json` and `benchmark.md` from those runs

The previous Opus 4.6 runs can be used as a reference for timing/token estimates, but the grading must be redone against the new fixture-grounded assertions.
