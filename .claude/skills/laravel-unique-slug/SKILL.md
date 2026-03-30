---
name: laravel-unique-slug
description: Implement reusable, production-safe unique slug generation in Laravel models such as Post, Article, KnowledgeBaseArticle, Page, Category, or similar content entities. Use when the project needs SEO-friendly slugs, route model binding by slug, or a shared slugging convention.
---

# Laravel Unique Slug Skill

Implement a reusable unique slug system for Laravel applications.

This skill is for content-oriented models such as:

- Post
- Article
- BlogPost
- KnowledgeBaseArticle
- Page
- Category
- DocumentationPage
- FAQArticle
- Similar Eloquent models that need human-readable unique URLs

## Goal

Build a reusable slugging system that:

1. Generates a URL-friendly slug from a source attribute such as `title` or `name`
2. Ensures uniqueness by appending numeric suffixes like `-1`, `-2`, `-3`
3. Works correctly during both create and update operations
4. Can exclude the current model during updates
5. Optionally considers soft-deleted rows when checking uniqueness
6. Is reusable across multiple models instead of duplicating logic
7. Is safe enough for production use when combined with a database unique index

## Reference behavior

Follow this slugging behavior:

- Base slug is produced with Laravel's `Str::slug()`
- First matching record uses the plain slug:
  - `my-first-post`
- Later matches use incremented suffixes:
  - `my-first-post-1`
  - `my-first-post-2`
  - `my-first-post-3`

When checking existing records, treat these as conflicts:

- exact match: `base-slug`
- suffixed variants: `base-slug-1`, `base-slug-2`, etc.

Only numeric suffixes count as slug sequence members.

Examples:

- `my-post`
- `my-post-1`
- `my-post-25`

These count toward the sequence.

Examples that do NOT count as numeric suffixes:

- `my-post-old`
- `my-post-copy`
- `my-post-final`

## Architecture requirements

Prefer a shared reusable implementation, not per-model duplication.

Use one of these approaches:

- preferred: `App\Models\Concerns\HasUniqueSlug`
- acceptable alternative: a trait plus a small support/service class such as `App\Support\SlugGenerator`

Default recommendation:

- trait contains the Eloquent integration and conventions
- optional support class contains the pure slug calculation logic

## Default conventions

Unless the existing codebase already has a stronger convention, use these defaults:

- slug column: `slug`
- source column: `title`
- route key: `slug`
- auto-generate on create: yes
- auto-regenerate on update: no by default
- allow per-model override: yes
- consider soft-deleted rows: yes when the model uses `SoftDeletes`

## Model-level configurability

The implementation should allow each model to customize:

- source field, such as `title` or `name`
- slug column name if different from `slug`
- whether slug is regenerated when the source field changes
- whether trashed rows are included in uniqueness checks
- whether manually provided slugs should be preserved

Use simple model properties or protected methods so each model can override behavior cleanly.

Example desired flexibility:

- `Post` may use `title`
- `Category` may use `name`
- `KnowledgeBaseArticle` may regenerate slug on title change
- `Page` may preserve manually edited slugs forever

## Query rules

When generating a unique slug:

1. Build the base slug from the source value using `Str::slug()`
2. If the result is empty, use a fallback such as `item`
3. Query existing rows where:
   - slug equals the base slug
   - OR slug matches `base-slug-%`
4. If updating an existing row, exclude the current row by primary key
5. If the model uses soft deletes and the project expects slug reservation, include trashed rows in the check
6. Parse only numeric suffixes using a strict regex
7. Determine the maximum numeric suffix
8. Return:
   - base slug if there are no matching rows
   - otherwise base slug plus the next numeric suffix

Use strict matching logic similar to:

- exact slug => suffix `0`
- `base-slug-7` => suffix `7`
- anything else => ignore

## Update behavior

Default update policy:

- do NOT regenerate slug automatically on every title/name change
- preserve stable URLs unless the project explicitly wants slug regeneration

If the model or project requires regeneration on update:

- only regenerate when the source attribute has actually changed
- exclude the current model from conflict checks
- preserve manual slug overrides unless the project explicitly says otherwise

## Manual slug support

If a slug is already explicitly set by the developer, seeder, factory, admin panel, or request logic:

- do not overwrite it automatically unless the project explicitly requires forced normalization

If the project already has an admin-facing editable slug field:

- preserve manual edits
- only generate automatically when slug is empty

## Database requirements

A database unique index on the slug column is required for production safety.

Application logic alone is not enough because concurrent requests may compute the same next slug.

At minimum:

- add a unique index for `slug`

If the project has multi-tenant scoping, localization, or parent-child nesting, adapt uniqueness accordingly. Examples:

- unique per site
- unique per tenant
- unique per locale
- unique per parent category

In those cases, scope both the application query and the database unique index consistently.

## Multi-tenant and scoped slugging

If the model belongs to a tenant, site, team, locale, or parent entity, support scoped uniqueness.

Examples:

- same slug can exist in different tenants
- same slug can exist in different locales
- same slug can exist under different parent categories

When scoped uniqueness is needed:

- include the scope columns in the query
- include the same scope in the unique database index
- document the chosen scope clearly in the implementation

## Soft delete behavior

Default behavior:

- if a model uses `SoftDeletes`, treat trashed rows as reserving their slug

This avoids reusing old URLs and reduces accidental collisions after restore.

If the project explicitly wants slug reuse after soft delete, make that a conscious override, not the default.

## Route model binding

If the model is public-facing and URLs should use slugs:

- implement `getRouteKeyName(): string`
- return `slug`

Only do this when it matches the project's routing conventions.

## Testing requirements

Add or update tests covering all important cases.

Minimum required tests:

1. generates base slug from title
2. generates `-1` for second duplicate
3. generates next suffix when multiple duplicates exist
4. ignores non-numeric suffix variants when computing the sequence
5. excludes the current record during update
6. respects soft-deleted rows when configured to include them
7. preserves manual slug if already provided
8. does not regenerate slug on update when regeneration is disabled
9. regenerates slug on update when regeneration is enabled
10. falls back safely when `Str::slug()` returns an empty string

If scoped uniqueness exists, add tests for the scope as well.

## Implementation quality rules

Do:

- keep the logic reusable
- keep the slug sequence parsing strict
- use expressive method names
- keep controller/service/request code free from duplicated slug logic
- use database constraints
- align tests with actual model behavior

Do not:

- duplicate the same slug code in every model
- rely on naive string splitting without strict suffix validation
- silently change existing public slugs unless the project requires it
- assume application-level checks alone prevent race conditions
- hardcode Team-specific naming in a general slug skill

## Recommended trait shape

Prefer a trait with methods conceptually similar to:

- `bootHasUniqueSlug()`
- `generateSlugOnCreate()`
- `generateSlugOnUpdate()`
- `generateUniqueSlug(...)`
- `getSlugColumnName()`
- `getSlugSourceColumnName()`
- `shouldRegenerateSlugOnUpdate()`
- `shouldIncludeTrashedInSlugCollisionCheck()`

Method names can differ if the project already has naming conventions.

## Expected deliverables

When implementing this skill in a Laravel project, the agent should usually produce:

1. a reusable trait in a shared namespace
2. any optional support class if needed
3. model updates for one or more content models
4. migration updates adding proper unique indexes
5. tests covering slug generation behavior
6. route model binding updates where appropriate

## Before changing code

Inspect the project for:

- existing slug columns
- existing route model binding behavior
- packages already handling slugs
- soft delete usage
- tenant/site/locale scope requirements
- admin forms that already expose editable slug fields

If the project already uses a slug package or established pattern, extend or align with it rather than introducing a conflicting parallel system.

## Output expectations

When asked to implement this skill:

- first inspect the current project structure
- identify candidate models
- determine whether uniqueness is global or scoped
- implement the shared slugging system
- wire it into the target models
- add database constraints
- add or update tests
- summarize exactly what changed and any assumptions made
