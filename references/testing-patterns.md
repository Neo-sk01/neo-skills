# Testing Patterns Reference

Lookup checklist for test design. Referenced by skills — not a skill itself.

---

## Test Structure

### Arrange-Act-Assert

Every test follows three phases:

```
// Arrange — set up the preconditions
const user = createUser({ role: "admin" });
const request = buildRequest({ userId: user.id });

// Act — perform the action under test
const result = await handler(request);

// Assert — verify the outcome
expect(result.status).toBe(200);
expect(result.body.role).toBe("admin");
```

### One Behavior Per Test

Each test verifies exactly one behavior. If a test name needs "and" in it, split it.

Bad: `test_login_validates_email_and_checks_password_and_returns_token`
Good: Three separate tests for validation, password check, and token return.

## Naming Conventions

Format: `test_[unit]_[scenario]_[expected result]`

Examples:
- `test_login_with_expired_token_returns_401`
- `test_create_order_with_empty_cart_raises_validation_error`
- `test_calculate_tax_for_exempt_item_returns_zero`

## What to Test

| Priority | What | Example |
|----------|------|---------|
| Always | Happy path | Valid input → correct output |
| Always | Error paths | Invalid input → proper error |
| Always | Edge cases | Empty, null, boundary values |
| Always | Changed code paths | Every line you modified |
| When relevant | Concurrency | Race conditions, deadlocks |
| When relevant | Integration | Cross-service contracts |

## What NOT to Test

- Private implementation details (test the public interface)
- Third-party library internals (trust them or don't use them)
- Trivial getters/setters with no logic
- Framework behavior (test YOUR code, not the framework)

## Mocking Strategy

### Mock at boundaries, not everywhere

Mock:
- External APIs and services
- Databases (in unit tests — use real DBs in integration tests)
- File system, network, time

Don't mock:
- Your own internal modules (test them through the public API)
- Simple data transformations
- Anything you could use a real instance for cheaply

### Mock smell: if you're mocking more than 2 things, the unit under test is doing too much.

## Test Data

- Use factory functions, not raw object literals
- Create only what the test needs — no default-heavy fixtures
- Make test data obviously fake: `user@test.example.com`, not `john@gmail.com`
- Reset state between tests — no shared mutable state
