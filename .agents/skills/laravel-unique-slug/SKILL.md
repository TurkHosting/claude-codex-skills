---
name: laravel-unique-slug
description: Implement reusable, production-safe unique slug generation in Laravel models such as Post, Article, KnowledgeBaseArticle, Page, Category, or similar content entities. Use when the project needs SEO-friendly slugs, route model binding by slug, or a shared slugging convention.
---

# Laravel Unique Slug

Apply this skill when a Laravel project needs stable, reusable, human-readable, unique slugs for content-oriented Eloquent models.

Use this skill for models such as:

- Post
- Article
- BlogPost
- KnowledgeBaseArticle
- Page
- Category
- DocumentationPage
- FAQArticle
- similar public-facing or content-oriented models

## When to Activate

Activate this skill when:

- creating slug logic for a new model
- refactoring duplicated slug logic into a shared implementation
- reviewing slug behavior for correctness or SEO safety
- adding route model binding by slug
- standardizing slug generation across multiple models

## Before Implementing

Inspect the project for:

- existing slug columns
- existing slug generation logic
- existing packages such as Spatie Sluggable or similar tools
- existing database unique indexes or constraints
- existing route model binding behavior
- soft delete usage
- tenant, site, locale, parent, or category scoping requirements
- admin forms or APIs that already expose an editable slug field

Do not introduce a conflicting slug system if the project already has a stronger established convention or an existing slug package.

## Goal

Build a reusable slugging system that:

1. generates a URL-friendly slug from a source attribute such as `title` or `name`
2. ensures uniqueness by appending numeric suffixes such as `-1`, `-2`, `-3`
3. works correctly during both create and update operations
4. excludes the current model during updates
5. handles soft-deleted rows intentionally
6. avoids duplicated slug logic across models
7. remains production-safe under concurrent writes when combined with database constraints

## Architecture Requirements

Prefer a shared reusable implementation. Do not duplicate slug logic per model.

Use one of these approaches:

- preferred: `App\Models\Concerns\HasUniqueSlug`
- acceptable: a trait plus a support class such as `App\Support\SlugGenerator`

Default recommendation:

- keep Eloquent integration and conventions in the trait
- keep pure slug calculation logic in an optional support class

## Default Conventions

Unless the existing codebase already has a stronger convention, use these defaults:

- slug column: `slug`
- source column: `title`
- route key: `slug`
- auto-generate on create: yes
- auto-regenerate on update: no by default
- allow per-model override: yes
- include soft-deleted rows in uniqueness checks when the model uses `SoftDeletes`

## Model-Level Configurability

Allow each model to customize:

- the source field, such as `title` or `name`
- the slug column name if it differs from `slug`
- whether slug regeneration is allowed on update
- whether trashed rows are included in uniqueness checks
- whether manually provided slugs are preserved
- whether uniqueness is global or scoped

Use simple model properties or protected methods so each model can override behavior cleanly.

## Reference Behavior

Generate slugs with Laravel's `Str::slug()`.

Use this sequence behavior:

- first matching record uses the plain slug: `my-first-post`
- later matches use incremented suffixes:
  - `my-first-post-1`
  - `my-first-post-2`
  - `my-first-post-3`

Treat these as conflicts:

- exact match: `base-slug`
- suffixed variants: `base-slug-1`, `base-slug-2`, and so on

Only numeric suffixes count as sequence members.

Count these:

- `my-post`
- `my-post-1`
- `my-post-25`

Ignore these:

- `my-post-old`
- `my-post-copy`
- `my-post-final`

## Query Rules

When generating a unique slug:

1. Build the base slug from the source value with `Str::slug()`.
2. If the slug is empty, use a fallback such as `item`.
3. Query existing rows where:
   - the slug equals the base slug
   - or the slug matches `base-slug-%`
4. Exclude the current model by primary key during updates.
5. Include trashed rows when the model uses `SoftDeletes` and slug reservation is enabled.
6. Parse suffixes with a strict numeric regex.
7. Determine the maximum numeric suffix.
8. Return:
   - the base slug when there are no conflicts
   - otherwise the base slug plus the next numeric suffix

Use strict matching logic:

- exact slug => suffix `0`
- `base-slug-7` => suffix `7`
- anything else => ignore

Do not rely on naive string splitting.

## Database Requirement (MANDATORY)

A unique index must exist on the slug column, or on the scoped slug columns when uniqueness is scoped.

Application-level checks alone are not sufficient.

Add database constraints that match the same uniqueness logic used by the application query.

## Concurrency Rule

Assume concurrent writes.

Do not assume application-level uniqueness checks are sufficient.

If a unique constraint violation occurs during slug creation, the implementation should either:

- retry slug generation safely
- or fail explicitly in a way the project can handle correctly

Do not present slug generation as production-safe without database-backed collision protection.

## Slug Stability Rule

Do not change a slug automatically on update by default.

Preserve stable URLs unless the project explicitly requires slug regeneration.

If a model opt-in requires regeneration on update:

- regenerate only when the source attribute actually changed
- exclude the current model from collision checks
- preserve manual slug overrides unless the project explicitly requires forced normalization

## Manual Slug Rule

If a slug is explicitly set by:

- a developer
- a seeder
- a factory
- an admin panel
- request handling logic

Do not overwrite it automatically.

Generate a slug automatically only when the slug field is empty, unless the project explicitly requires forced normalization.

## Soft Delete Rule

If the model uses `SoftDeletes`, treat trashed rows as reserving their slug by default.

Do not reuse the slug of a soft-deleted record unless the project explicitly chooses reuse.

This reduces accidental URL collisions after restore.

## Scoped Slugs

If the model belongs to a scope such as:

- tenant
- site
- locale
- parent category
- team

Scope uniqueness consistently.

When scoped uniqueness is required:

- include the scope columns in the application query
- include the same scope columns in the database unique index
- document the chosen scope clearly in the implementation

Do not mix global uniqueness in code with scoped uniqueness in the database, or the reverse.

## Routing Rule

If the model is public-facing and the project intends public URLs to use slugs:

- implement `getRouteKeyName(): string`
- return the slug column name
- align the route model binding with the chosen slug strategy

Do not change route binding blindly if the project already uses a different established routing convention.

## Implementation Quality Rules

Do:

- keep the logic reusable
- keep suffix parsing strict
- use expressive method names
- keep controller, service, request, and form code free from duplicated slug logic
- use database constraints
- align tests with actual model behavior

Do not:

- duplicate the same slug code in every model
- rely on weak suffix parsing
- silently change existing public slugs unless the project explicitly requires it
- assume application-level checks prevent race conditions
- introduce a conflicting slug system alongside an existing package or established convention

## Recommended Trait Shape

Prefer a trait with methods conceptually similar to:

- `bootHasUniqueSlug()`
- `generateSlugOnCreate()`
- `generateSlugOnUpdate()`
- `generateUniqueSlug(...)`
- `getSlugColumnName()`
- `getSlugSourceColumnName()`
- `shouldRegenerateSlugOnUpdate()`
- `shouldIncludeTrashedInSlugCollisionCheck()`
- `getSlugScopeColumns()`

Method names may differ if the project already has naming conventions.

## Testing Requirements

Add or update tests that cover:

1. base slug generation from title
2. `-1` generation for the second duplicate
3. next suffix generation when multiple duplicates exist
4. ignoring non-numeric suffix variants
5. excluding the current record during update
6. respecting soft-deleted rows when slug reservation is enabled
7. preserving a manual slug when already provided
8. not regenerating on update when regeneration is disabled
9. regenerating on update when regeneration is enabled
10. falling back safely when `Str::slug()` returns an empty string

If uniqueness is scoped, add tests for the chosen scope.

If route model binding changes, add or update tests for routing behavior.

## Enforcement Rule

If the existing slug logic is inconsistent, duplicated, or broken:

- refactor it
- do not preserve broken logic just because it already exists

If the project already has a stronger established slug convention or an existing slug package, align with that system instead of creating a parallel implementation.

## Agent Output Requirements

The agent must:

- inspect the current slug system before changing code
- identify whether uniqueness is global or scoped
- add or verify matching database constraints
- preserve slug stability by default
- preserve manual slugs unless explicitly told otherwise
- handle soft deletes intentionally
- implement or align route model binding when appropriate
- add or update tests
- summarize the exact implementation choices and assumptions

## Expected Deliverables

When applying this skill, usually produce:

1. a reusable trait in a shared namespace
2. an optional support class if needed
3. model updates for one or more content models
4. migration updates adding the proper unique index or scoped unique index
5. tests covering slug behavior and edge cases
6. route model binding updates where appropriate

## Final Check

Before finishing, confirm:

- the slug logic is shared instead of duplicated
- uniqueness is enforced both in code and in the database
- update behavior preserves stable URLs by default
- manual slugs are preserved
- soft deletes are handled intentionally
- scoped uniqueness, if needed, is implemented consistently
- routing matches the chosen slug strategy
- tests cover the important behaviors

If any item fails, refactor.
