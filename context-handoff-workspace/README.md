# Context Handoff — Eval Workspace

## Current State

**Eval spec**: [`evals/evals.json`](../evals/evals.json) — fixture-grounded (3 evals, 20 assertions)
**Fixtures**: [`evals/fixtures/`](../evals/fixtures/) — HANDOFF.md, PLAN.md variants, package.json
**Trigger eval**: [`trigger-eval.json`](trigger-eval.json) — 25 queries (10 should-trigger, 15 should-not-trigger, including filename-only false-positives)

## Benchmark

**Current**: [`iteration-1/benchmark.md`](iteration-1/benchmark.md) — **claude-opus-4-7**, fixture-grounded, 2026-04-21

| Metric | With Skill | Without Skill | Delta |
|--------|-----------|---------------|-------|
| Pass Rate | 100.0% ± 0.0% | 58.7% ± 21.4% | +41.3% |
| Time | 92.3s ± 15.7s | 32.7s ± 1.9s | +59.6s |
| Tokens | 41,680 ± 2,123 | 22,611 ± 2,111 | +19,069 |

Per-eval: kickoff 100% vs 33%, resume 100% vs 86%, handoff-gen 100% vs 57%. See [`iteration-1/benchmark.md`](iteration-1/benchmark.md) for full breakdown, honest caveats, and analyst notes.

**Superseded**: [`iteration-0-pregrounding/`](iteration-0-pregrounding/) — old runs against `files: []` evals. Outputs hallucinated wrong dependencies, checkpoint IDs, and next-task state. See [`iteration-0-pregrounding/INVALIDATED.md`](iteration-0-pregrounding/INVALIDATED.md).

## Known Limitations of iteration-1

- **N=1 per configuration.** Pass-rate variance is across 3 evals, not 3 runs of the same eval. Multi-run comparison is still pending.
- **Fixture strength inflates baseline.** The resume fixture is nearly optimal — a strong model extracts correct facts without the skill. A degraded-fixture eval would show a wider gap.
- **Single grader.** Grading is manual, single-grader. A second grader (model or human) would be a worthwhile follow-up on high-stakes assertions.

## To Run a New Iteration

1. For each eval in `evals/evals.json`, load the referenced fixture files from `evals/fixtures/` as inputs
2. Spawn the agent in two configurations per eval: `with_skill` (SKILL.md loaded) and `without_skill` (baseline)
3. Grade each output against the assertions in `evals/evals.json` — the assertions require fixture-derived evidence (specific versions, checkpoint IDs, reload order, API contracts, human blockers)
4. Aggregate into `iteration-N/benchmark.json` and generate `iteration-N/benchmark.md` from it
