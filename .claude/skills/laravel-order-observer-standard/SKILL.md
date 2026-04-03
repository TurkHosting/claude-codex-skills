---
name: laravel-order-observer-standard
description: Enforce consistent ordering (position or order) management using Laravel Observers and Filament integration. Use when implementing sortable models, automatic ordering, scoped ordering, or drag-and-drop reordering.
---

# Laravel Order / Position Observer Standard

Apply this skill when a Laravel project needs consistent ordering with observers and optional Filament integration.

## Activation

Use this skill when:

- implementing sortable records
- managing `position` or `order`
- enabling drag-and-drop ordering in Filament
- refactoring duplicated or conflicting ordering logic
- handling parent-scoped ordering

## Core Rules

- Detect the existing ordering column before changing anything.
- Use only one ordering column across the project: `position` or `order`.
- Keep ordering logic centralized in observers.
- Do not introduce a second ordering system in controllers, actions, or form handlers.
- Use the references below instead of expanding `SKILL.md`.

## References

- `references/column-and-registration.md`
- `references/global-and-scoped-ordering.md`
- `references/observer-rules.md`
- `references/filament-integration.md`
- `references/edge-cases.md`
