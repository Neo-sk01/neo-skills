---
name: shipping-and-launch
description: Pre-launch verification, integration checkpoints, and deployment procedures. Use when ready to ship a feature or close out an epic.
---

# Shipping and Launch

## Overview

Ship with confidence. Before any code leaves the branch, run the full integration checkpoint, get a cross-model review, verify documentation, and confirm rollback procedures. Shipping is a process, not a moment.

## When to Use

- Feature implementation is complete
- All plan tasks are done
- Ready to merge and/or deploy
- Closing out an epic

## When NOT to Use

- Still implementing — finish first
- Tests failing — fix first

## Integration Points

**Entire:** All documentation generated, session archived. Entire checkpoints provide the full history.

**Codex:** Final review on the full branch diff:
```
/codex:review --base main --background
```

**Context7:** Final verification on any library APIs used — confirm installed versions match what was developed against.

## Core Process

### 1. Integration checkpoint

Run the full validation suite:

```
- [ ] All tests pass (full suite, not just new tests)
- [ ] Build succeeds
- [ ] Lint/format passes
- [ ] Cross-system dependencies checked (from CODEBASE.md):
      - API contracts match?
      - DB migrations consistent?
      - Third-party integrations work?
      - Environment variables present?
```

### 2. Codex final review

Request cross-model review on the full diff:
```
/codex:review --base main --background
```

While waiting for Codex, continue with steps 3-5.

### 3. Documentation check

- [ ] README updated if features added
- [ ] API documentation for new endpoints
- [ ] CODEBASE.md updated if architecture changed
- [ ] Migration guide if breaking changes
- [ ] Inline comments match codebase style

### 4. Pre-launch checklist

- [ ] All acceptance criteria from spec are met
- [ ] No TODO/FIXME/HACK comments in shipped code
- [ ] No console.log/print debugging statements left
- [ ] No hardcoded test/development values
- [ ] Error messages are user-friendly (no stack traces to end users)
- [ ] Feature flags configured correctly (if applicable)

### 5. Rollback plan

Before deploying, document:
- How to roll back (specific commands or procedures)
- What data might be affected (migrations, caches)
- Who to notify if rollback is needed
- How to verify the rollback worked

### 6. Review Codex results

Check `/codex:status` and `/codex:result`. Address any findings:
- CRITICAL: fix before merge
- HIGH: fix before deploy
- MEDIUM: create follow-up ticket
- LOW: note for next session

### 7. Merge and tag

```bash
git checkout main
git merge --no-ff feature-branch
git tag -a v[version] -m "[description]"
```

### 8. Transition to context-handoff

After shipping, invoke `context-handoff` to archive the session and prepare the handoff document.

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "Tests pass, ship it" | Tests are necessary but not sufficient. Integration checkpoint catches what unit tests miss. |
| "We can fix documentation later" | Documentation debt compounds. Update it now while context is fresh. |
| "Codex review is too slow" | Start it early (step 2) and continue with other checks while it runs. |
| "We don't need a rollback plan" | You need a rollback plan precisely when you think you don't. |

## Red Flags

- Shipping without running the full test suite
- No Codex review on multi-day features
- Documentation not updated
- No rollback plan
- TODO/FIXME left in shipped code
- Skipping context-handoff after shipping

## Verification

- [ ] Full integration checkpoint passed
- [ ] Codex review requested and findings addressed
- [ ] Documentation updated
- [ ] Pre-launch checklist completed
- [ ] Rollback plan documented
- [ ] Context-handoff invoked to archive the session
