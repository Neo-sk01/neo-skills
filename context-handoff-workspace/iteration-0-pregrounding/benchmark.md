# ⚠ Benchmark INVALIDATED — Pre-Grounding

**Status**: This benchmark has been invalidated. See `INVALIDATED.md` in this directory for the full explanation.

**Model**: claude-opus-4-6
**Date**: 2026-04-12T23:00:00Z (original run)
**Invalidated**: 2026-04-21 (after eval spec was updated to require fixture grounding)

## Why

The outputs in this directory were generated against an earlier `evals/evals.json` where every eval had `files: []`. The model was free to invent the project, checkpoint IDs, and next-task state. Those outputs do not pass the current fixture-grounded assertions.

Specifically, the `with_skill` outputs:
- Hallucinated `next@14.2.0` / `stripe@14.0.0` while the fixture specifies `next@15.1.0` / `stripe@17.2.0`
- Loaded the wrong files in the wrong order relative to the fixture HANDOFF.md's Compact Reload Packet
- Invented checkpoint IDs `abc1234` / `def5678` instead of the fixture's `ck_7f8e9a2b` / `ck_5e6f7a8b`
- Changed the API contract for the upgrade endpoint (`PUT` + `always_invoice` vs fixture's `POST` + `create_prorations`)
- Misrouted the next session to an "admin dashboard" instead of task #7 (E2E tests, blocked on Stripe CI keys)

Publishing a pass rate against these outputs would advertise exactly the failure mode this skill exists to prevent.

## What Previously Shipped (pre-invalidation, for reference only)

Under the old prose-only assertions, the original summary was:

| Metric | With Skill | Without Skill | Delta |
|--------|-----------|---------------|-------|
| Pass Rate | 100.0% ± 0.0% | 15.1% ± 11.7% | +84.9% |
| Time | 140.1s ± 41.2s | 57.0s ± 3.4s | +83.1s |
| Tokens | 26,410 ± 4,335 | 13,297 ± 92 | +13,113 |

These numbers should **not** be cited as evidence of skill quality until a re-run against the grounded fixtures reproduces them.

## Next Required Action

Run a fresh iteration-1 against the grounded `evals/evals.json`:

- 3 evals × 2 configurations × N runs per configuration
- Inputs: load the fixture files from `evals/fixtures/` per the `files[]` in each eval
- Grading: assertions in `evals/evals.json` require exact fixture-derived facts (versions, checkpoint IDs, reload order, API contracts, human blockers)

Only after that run produces genuine pass rates should a new `benchmark.md` / `benchmark.json` be generated.
