---
name: laravel-unique-slug
description: Implement reusable, production-safe unique slug generation in Laravel models such as Post, Article, KnowledgeBaseArticle, Page, Category, or similar content entities. Use when the project needs SEO-friendly slugs, route model binding by slug, or a shared slugging convention.
---

# Laravel Unique Slug

Apply this skill when a Laravel project needs stable, reusable, unique slugs for content-oriented Eloquent models.

## Activation

Use this skill when:

- creating slug logic for a new model
- refactoring duplicated slug logic into a shared implementation
- reviewing slug behavior for correctness
- adding route model binding by slug
- standardizing slug generation across multiple models

## Core Rules

- Inspect the existing slug system before changing it.
- Do not introduce a conflicting slug package or parallel slug architecture.
- Prefer shared slug logic instead of duplicating logic in every model.
- Keep slugs stable by default on update.
- Use the references below for the full rules.

## References

- `references/architecture-and-conventions.md`
- `references/generation-rules.md`
- `references/database-and-concurrency.md`
- `references/scoped-and-routing.md`
- `references/testing.md`
