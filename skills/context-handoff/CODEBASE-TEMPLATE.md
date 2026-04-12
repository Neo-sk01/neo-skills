# Codebase Template

Use this template when generating CODEBASE.md during Phase 0.

---

```markdown
# Codebase: {repo_name}
Last updated: {date} — Session {n}

## Architecture
{2-3 paragraphs: entry points, service boundaries, data flow,
key abstractions and their relationships. How the system works
at a high level.}

## File Structure
{Annotated tree of key directories. Not every file — just the
directories that matter and what lives in them.}

```
src/
├── api/          # REST endpoints, route handlers
├── services/     # Business logic layer
├── models/       # Database models / ORM definitions
├── middleware/    # Auth, logging, error handling
├── utils/        # Shared helpers
├── config/       # Environment config, feature flags
└── tests/        # Test files mirroring src/ structure
```

## Conventions
{Concrete, follow-these rules for anyone writing code here.}

- **Naming:** {camelCase/snake_case/PascalCase — be specific per context}
- **File organization:** {one component per file, barrel exports, etc.}
- **Error handling:** {custom error classes, error boundaries, try-catch patterns}
- **Logging:** {structured logging, log levels, what to log}
- **Documentation:** {JSDoc/docstrings/none — specify the style}
- **Import ordering:** {external → internal → relative, etc.}

## Test Patterns
- **Unit tests:** {framework, location, naming convention}
- **Integration tests:** {framework, how they're run, fixtures}
- **E2E tests:** {framework, if they exist}
- **Mocking strategy:** {what's mocked, what's not, mock library}
- **Test data:** {factories, fixtures, seeds}

## Build & Deploy
- **Build command:** `{command}`
- **Test command:** `{command}`
- **Lint command:** `{command}`
- **CI/CD:** {pipeline description, key stages}
- **Environments:** {dev, staging, prod — how they differ}

## Cross-System Dependency Map
| System | Type | Interface | Contract | Health Check |
|---|---|---|---|---|
| {PostgreSQL} | Database | {prisma/knex/raw} | {schema version} | `{check command}` |
| {Redis} | Cache | {ioredis/redis} | {sentinel/standalone} | `{check command}` |
| {Stripe} | Payment API | REST + webhooks | API version {v} | {test mode ping} |
| {Auth0} | Auth provider | OIDC + JWKS | tenant: {id} | {token validation} |
| {S3} | Storage | AWS SDK v3 | bucket: {name} | {list objects} |
| {SendGrid} | Email | REST API | API key auth | {test send} |

Dependencies modified this epic: {list — focus for integration checkpoints}

## Dependency Status (Context7 Audit)
| Library | Installed | Current | Breaking Changes? | Confidence |
|---|---|---|---|---|
| {next.js} | {14.2.0} | {15.1.0} | {Yes — App Router changes} | {HIGH} |
| {prisma} | {5.22.0} | {5.22.0} | {No} | {HIGH} |
| {stripe} | {14.0.0} | {17.2.0} | {Yes — webhook sig change} | {MEDIUM} |

## Domain Knowledge
{Business logic hotspots. Where the "why" lives in the code.
Things that aren't obvious from reading the code alone.}

- {e.g., "The pricing calculation in src/services/pricing.ts has special
  logic for enterprise tiers that was added for Client X in Q3 2024"}
- {e.g., "The auth middleware skips token validation on /api/webhooks
  because Stripe sends unsigned webhook events in test mode"}

## Known Tech Debt
| Area | Issue | Why It Exists | Risk |
|---|---|---|---|
| {auth} | {JWT validation uses deprecated library} | {Migration blocked on v2 API} | {Medium — works but no security patches} |
| {database} | {N+1 queries in user listing} | {Premature optimization avoided} | {Low until >10k users} |

## Environment Variables
| Variable | Purpose | Required | Where Set |
|---|---|---|---|
| {DATABASE_URL} | {PostgreSQL connection} | Yes | {.env, CI secrets} |
| {STRIPE_SECRET_KEY} | {Payment processing} | Yes | {.env, CI secrets} |
| {REDIS_URL} | {Cache connection} | No (defaults to localhost) | {.env} |
```
