# Database And Concurrency

## Database Requirement

A unique index must exist for the same uniqueness logic used in code.

- global uniqueness: unique index on the slug column
- scoped uniqueness: unique index on slug plus scope columns

Application-level checks alone are not enough.

## Concurrency

Assume concurrent writes.

If a unique constraint violation happens during slug generation, the implementation should:

- retry safely
- or fail explicitly in a way the project can handle

Do not present the system as production-safe without database-backed protection.
