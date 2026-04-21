# Skill Benchmark: context-handoff (iteration-1, fixture-grounded)

**Model**: claude-opus-4-7
**Date**: 2026-04-21T00:00:00Z
**Evals**: new-project-kickoff, mid-session-resume, budget-pressure-handoff (1 run each per configuration)
**Grounded**: yes — all evals use fixtures from `evals/fixtures/`

## Summary

| Metric | With Skill | Without Skill | Delta |
|--------|-----------|---------------|-------|
| Pass Rate | 100.0% ± 0.0% | 58.7% ± 21.4% | +41.3% |
| Time | 92.3s ± 15.7s | 32.7s ± 1.9s | +59.6s |
| Tokens | 41,680 ± 2,123 | 22,611 ± 2,111 | +19,069 |

## Per-Eval Breakdown

| Eval | With Skill | Without Skill | Differential |
|------|-----------|---------------|--------------|
| new-project-kickoff | 6/6 (100%) | 2/6 (33.3%) | +66.7% |
| mid-session-resume | 7/7 (100%) | 6/7 (85.7%) | +14.3% |
| budget-pressure-handoff | 7/7 (100%) | 4/7 (57.1%) | +42.9% |
| **Total assertions** | **20/20 (100%)** | **12/20 (60.0%)** | **+40.0%** |

## Token & Time Breakdown

| Eval | Config | Tokens | Time | Tool Calls |
|------|--------|--------|------|------------|
| new-project-kickoff | with_skill | 40,143 | 111.8s | 6 |
| new-project-kickoff | without_skill | 21,133 | 31.9s | 1 |
| mid-session-resume | with_skill | 44,682 | 91.8s | 7 |
| mid-session-resume | without_skill | 25,597 | 35.3s | 2 |
| budget-pressure-handoff | with_skill | 40,214 | 73.4s | 6 |
| budget-pressure-handoff | without_skill | 21,103 | 30.9s | 1 |

## What Each Config Got Right and Wrong

### With Skill (all 100%)
- **Grounded artifacts**: produces CODEBASE.md with fixture versions, Compact Reload Packets, Context Budget sections, Human Tasks Queues tied to fixture blockers.
- **Protocol compliance**: explicit reload-order enumeration, runs `entire status`, references fixture checkpoint IDs verbatim, follows fixture's archaeology escalation rule.
- **Honesty**: when Context7 is unavailable, marks confidence as NONE rather than fabricating HIGH results. Uses placeholders like `<task-6-sha>` when data is genuinely unknown rather than inventing SHAs.

### Without Skill (60% overall)
- **Correct on content extraction** when the fixture is well-structured: picks up correct task numbers, checkpoint IDs, API contracts from HANDOFF.md prose.
- **Fails on structural artifacts**: no CODEBASE.md, no EPIC.md, no Compact Reload Packet, no Context Budget, no compression pass.
- **No Context7 workflow**: cites API details from training data without confidence scoring — the exact failure mode the skill is designed to prevent.
- **Non-discriminating assertions** (passed in both): `handoff-references-fixture-task-progress`, `continues-task-5-from-fixture`, `handoff-next-action-grounded`, `acknowledges-fixture-human-task`. A good model reading a well-structured HANDOFF.md fixture can extract these without the skill.

## Comparison to Pre-Grounding Run (iteration-0)

The pre-grounding run reported 100% vs 15.1%. Under the fixture-grounded evals, the baseline rose to 60% because:
1. The old evals had `files: []`, so the baseline couldn't ground anything — it fabricated. New evals give the baseline the same fixtures the skill has, so surface-level content extraction is fairer.
2. The fixture HANDOFF.md is very well-structured. A strong model can extract correct facts even without the skill's protocol.

The 60% baseline is a more honest reflection of what a well-written fixture does for the model. The remaining 40% is the skill's genuine contribution: structural artifacts, explicit protocols, Context7 workflow, compression pass.

## Honest Caveats

- **N=1 per configuration.** Variance estimates are from 3 evals, not 3 runs of the same eval. A full statistical comparison needs multiple runs per (eval, config).
- **Fixture strength matters.** The resume fixture is nearly optimal (100% vs 86% baseline) — it undersells the skill's robustness. A degraded/partial-fixture eval would show a wider gap.
- **Grading is manual.** Single-grader bias possible. Future iterations should add a second grader (another model or human) for high-stakes assertions.
- **Without-skill baseline had full tool access** — it could read fixtures. This is the correct comparison for "does the skill add value over vanilla Claude in this repo" but not for "does the skill add value over a model with no tools".

## Data Integrity

This file is generated from `benchmark.json`. Invariants:
- `runs_per_configuration`: 1 (matches `metadata.runs_per_configuration`)
- Total evals: 3 (matches `metadata.evals_run` length)
- Total assertions: 20 with-skill, 20 without-skill
- Summary pass rates, token counts, times match source JSON within rounding
- `grounded_fixtures: true` confirms evals reference `evals/fixtures/`
