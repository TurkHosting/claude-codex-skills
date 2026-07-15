# Implementation Standard

This reference defines the stricter consistency conventions layered **on top of** the package basics (which `medialibrary-development` covers) for Spatie Media Library usage in Laravel and Filament projects.

## Core Architecture

- Manage all images through Spatie Media Library.
- Do not store image paths or filenames in model columns when the image belongs in a media collection.
- Use the `media` table as the source of truth.
- Use single-file collections by default.
- Generate exactly two primary conversions per image size unless a justified variation exists:
  - `optimized`
  - `web`
- Use `Fit::Crop` for conversions.
- **Queueing is a per-project decision:** prefer queued conversions when the project runs a queue worker (do not add `nonQueued()`); use `->nonQueued()` (or `queue_conversions_by_default => false`) when it does not. Match the project's actual queue setup.
- **Seeders (any project):** generate demo images synchronously — no worker runs during seeding — via `->nonQueued()` or a config override in seeder scope.
- Use public visibility for uploads.
- Accept JPEG and PNG by default.
- Define dimensions as model constants and reuse them everywhere.

## Single Source Of Truth

Image dimensions should be declared only in the model as constants.

Reference those constants from:

- media conversions
- Filament validation rules
- helper text
- seeders

Do not duplicate image dimensions across forms, resources, and helpers.

## Single Collection Example

The `HasMedia` / `InteractsWithMedia` / `registerMediaCollections` scaffolding is standard package setup (see `medialibrary-development`); what this example demonstrates is the **conversion convention** — two conversions, shared model constants, `Fit::Crop`, and `webp` only on `web`:

```php
use Spatie\MediaLibrary\HasMedia;
use Spatie\MediaLibrary\InteractsWithMedia;
use Spatie\MediaLibrary\MediaCollections\Models\Media;
use Spatie\Image\Enums\Fit;

class Slider extends Model implements HasMedia
{
    use InteractsWithMedia;

    public const IMAGE_WIDTH = 1500;
    public const IMAGE_HEIGHT = 508;

    public function registerMediaCollections(): void
    {
        $this->addMediaCollection('image')->singleFile();
    }

    public function registerMediaConversions(?Media $media = null): void
    {
        $this->addMediaConversion('optimized')
            ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT);

        $this->addMediaConversion('web')
            ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT)
            ->format('webp');
    }
}
```

## Multiple Collections

When a model has multiple media collections, bind conversions explicitly with `performOnCollections()`.

Use predictable names such as:

- `optimized`
- `web`
- `og-optimized`
- `og-web`
- `thumb-optimized`
- `thumb`

## Strict Conversion Rules

- Do not call `->format()` on `optimized`.
- Always call `->format('webp')` on `web`.
- Keep `optimized` and `web` dimensions identical for the same image size.
- Use `performOnCollections()` when multiple collections share a model.

## Anti-Patterns

- storing media-library image paths in normal columns
- hardcoding dimensions in multiple layers
- inventing random conversion names
- mixing seed-time and runtime image concerns inconsistently
