# Decisions Log

## 2026-04-12

| Decision | Rationale | Reversible? |
|---|---|---|
| Enable Entire locally in `neo-skills` during bootstrap | The repo documents an Entire-backed handoff workflow, so local testing should support real `entire status` preflight and future checkpoints | yes |
| Treat missing resume artifacts as a Phase 0 bootstrap rather than synthetic recovery | The workflow depends on persisted context, not invented continuity | yes |
| Keep the initial reload packet compact and route deeper context through repo docs | Clean-context resume is the skill's core promise | yes |
