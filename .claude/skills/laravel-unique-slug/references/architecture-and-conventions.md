# Architecture And Conventions

## Preferred Shape

Use a shared reusable implementation.

Preferred:

- `App\Models\Concerns\HasUniqueSlug`

Acceptable:

- a trait plus a support class such as `App\Support\SlugGenerator`

## Default Conventions

- slug column: `slug`
- source column: `title`
- route key: `slug`
- auto-generate on create: yes
- auto-regenerate on update: no by default
- include soft-deleted rows in uniqueness checks when slug reservation is enabled

## Model-Level Configurability

Allow models to override:

- source field
- slug column
- regenerate-on-update behavior
- include-trashed behavior
- manual-slug preservation
- scoped uniqueness
