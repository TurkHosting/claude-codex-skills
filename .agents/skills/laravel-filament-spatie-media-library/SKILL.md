---
name: laravel-filament-spatie-media-library
description: Enforce a consistent Spatie Media Library implementation across Laravel and Filament projects. Use when adding or refactoring model media collections, conversions, Filament upload fields, image table columns, API resource image URLs, helper utilities, or seeders. Ignore frontend framework specifics and focus on Laravel, Filament, and media architecture consistency.
---

# Laravel + Filament Spatie Media Library

Apply this skill when a Laravel or Filament project needs a consistent media architecture built on Spatie Media Library.

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
