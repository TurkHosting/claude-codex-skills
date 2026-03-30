---
name: laravel-filament-spatie-media-library
description: Enforce a consistent Spatie Media Library implementation across Laravel and Filament projects. Use when adding or refactoring model media collections, conversions, Filament upload fields, image table columns, API resource image URLs, helper utilities, or seeders. Ignore frontend framework specifics and focus on Laravel, Filament, and media architecture consistency.
---

# Laravel + Filament Spatie Media Library Skill

Apply this skill when a Laravel or Filament project needs a consistent media architecture built on Spatie Media Library.

## When to Activate

Activate this skill when:

- adding image support to a model
- refactoring inconsistent media handling
- creating or updating Filament upload fields
- creating or updating Filament image columns
- updating API resources to expose media URLs
- writing or fixing media seeders
- reviewing whether a project follows a consistent Spatie-based media standard

Ignore frontend framework specifics. Focus on Laravel, Filament, and media architecture consistency.

## Before Implementing

Inspect the project for:

- existing Spatie Media Library usage
- existing collection naming
- existing conversion naming
- existing file visibility settings
- existing model constants for dimensions
- existing Filament upload and table components
- existing API resource media keys
- existing placeholder or fake image helpers
- singleton Filament pages that bind to one model instance

Do NOT introduce a parallel media architecture if the project already has a stronger established pattern.

Read the detailed reference when needed:

- `../../../docs/spatie-media-library-standard.md`

## Core Rules

- Manage all model images through Spatie Media Library.
- Use the `media` table as the source of truth.
- Do NOT store image paths or filenames in normal model columns when the image belongs in a media collection.
- Make collections single-file by default.
- Define image dimensions only as model constants.
- Reference model constants from conversions, Filament validation, helper text, and seeders.
- Use exactly two primary conversions per image size by default:
  - `optimized` for cropped or resized original-format output
  - `web` for cropped or resized WebP output
- Use `Fit::Crop` for conversions.
- Use `nonQueued()` for conversions unless the project explicitly requires a queue-based media pipeline.
- Use public visibility for uploads.
- Accept only JPEG and PNG by default.

## Model Pattern

A model using this skill MUST:

- implement `HasMedia`
- use `InteractsWithMedia`
- define width and height constants in uppercase snake case
- register each collection explicitly
- use `->singleFile()` unless the project explicitly requires gallery-like behavior

Use constant names like:

- `IMAGE_WIDTH`
- `IMAGE_HEIGHT`
- `HEADER_IMAGE_WIDTH`
- `HEADER_IMAGE_HEIGHT`
- `OG_IMAGE_WIDTH`
- `OG_IMAGE_HEIGHT`

## Conversion Pattern

For a single collection:

- use `optimized`
- use `web`

For extra sizes or extra collections, use predictable prefixes such as:

- `thumb-optimized`
- `thumb`
- `og-optimized`
- `og-web`

Apply these rules:

- Do NOT call `->format()` on `optimized`
- Always call `->format('webp')` on `web`
- Keep `optimized` and `web` dimensions identical for the same image size
- Use `performOnCollections()` when a model has multiple media collections

## Filament Upload Pattern

Use `SpatieMediaLibraryFileUpload` for image uploads.

Each upload field MUST include:

- `->collection('...')`
- `->conversion('web')`
- `->label('...')`
- `->helperText(...)` built from model constants
- `->rule(Rule::dimensions()...)` built from model constants
- `->image()`
- `->acceptedFileTypes(['image/jpeg', 'image/png'])`
- `->maxSize(...)`
- `->visibility('public')`
- `->openable()`

Use `columnSpanFull()` when the layout needs a full-width upload field.

If the field belongs to a singleton model edited through a Filament Page, bind the upload field explicitly to that singleton model instance.

## Filament Table Pattern

Use `SpatieMediaLibraryImageColumn` for media table columns.

Usually include:

- `->collection('...')`
- `->conversion('web')`
- `->label('...')`

Optionally include:

- `->imageSize(...)`
- `->circular()` for avatar-like images

Do NOT guess collection or conversion names. Match the model's actual media contract.

## Seeder Pattern

Media seeders MUST:

- use model constants for dimensions
- check `hasMedia()` before attaching media
- attach media to the correct collection
- remain idempotent across repeated runs

Prefer a local placeholder generation helper over unstable external image services.

## API Resource Pattern

If the model is API-facing, expose predictable media keys for each collection:

- `{collection}`
- `{collection}_optimized`
- `{collection}_web`

Do NOT invent inconsistent API key names per model.

## Enforcement Rules

If the existing implementation is inconsistent, refactor it toward this standard.

Correct issues such as:

- hardcoded dimensions outside the model
- missing `optimized` or `web` conversions
- upload fields without `visibility('public')`
- random conversion names
- duplicated seed media attachments
- path columns mixed with media collections for the same image concern
- missing `singleFile()` on collections that should hold only one image

## Anti-Patterns

Do NOT:

- store image paths in normal model columns for media-library-managed images
- hardcode dimensions in multiple layers
- omit model constants
- call `format()` on `optimized`
- use different dimensions for `optimized` and `web` for the same image size
- allow unsupported upload formats by default
- omit `visibility('public')`
- omit `nonQueued()` unless explicitly justified
- create ad hoc upload logic outside Spatie Media Library
- duplicate media attachments on every seeder run

## Agent Output Requirements

The agent MUST:

1. inspect the current media structure
2. normalize model constants, collections, and conversions
3. update Filament upload fields if relevant
4. update Filament image columns if relevant
5. update seeders if relevant
6. update API resources if relevant
7. preserve consistent naming and architecture
8. summarize changes and assumptions
