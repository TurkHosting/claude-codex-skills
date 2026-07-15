---
name: laravel-filament-spatie-media-library
description: Enforce a consistent Spatie Media Library implementation across Laravel and Filament projects. Use when adding or refactoring model media collections, conversions, Filament upload fields, image table columns, API resource image URLs, helper utilities, or seeders. Ignore frontend framework specifics and focus on Laravel, Filament, and media architecture consistency.
---

# Laravel + Filament Spatie Media Library

Apply this skill when a Laravel or Filament project needs a consistent media architecture built on Spatie Media Library.

## Relationship to `medialibrary-development`

This skill is a **layer on top of the Spatie package's own mechanics — it does not repeat them.** When `spatie/laravel-medialibrary` is a project dependency, Laravel Boost automatically loads the package author's `medialibrary-development` skill, which owns the fundamentals: implementing `HasMedia` / `InteractsWithMedia`, adding and retrieving media, defining collections and conversions, responsive images, and URL/path generation. Defer to it for any package-API question.

What this skill adds on top:

- **Filament integration** — upload fields and image columns (`medialibrary-development` covers none of this).
- **Cross-project consistency conventions** — conversion naming, dimension single-source-of-truth, API resource keys, and seeder rules.

## Activation

Use this skill when:

- adding image support to a model
- refactoring inconsistent media handling
- creating or updating Filament upload fields
- creating or updating Filament image columns
- updating API resources to expose media URLs
- writing or fixing media seeders

## Core Rules

- Inspect the current media structure before changing naming or conversions.
- Keep Spatie Media Library as the single source of truth for managed images.
- Do not introduce a parallel image-path architecture if the project already uses Spatie.
- Use the references below instead of expanding `SKILL.md`.

## References

- `references/model-pattern.md`
- `references/conversions.md`
- `references/filament-integration.md`
- `references/seeder-and-api.md`
- `references/implementation-standard.md`
