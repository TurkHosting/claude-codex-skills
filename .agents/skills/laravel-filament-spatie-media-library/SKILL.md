---
name: laravel-filament-spatie-media-library
description: Enforce a consistent Spatie Media Library implementation across Laravel and Filament projects. Use when adding or refactoring model media collections, conversions, Filament upload fields, image table columns, API resource image URLs, helper utilities, or seeders. Ignore frontend framework specifics and focus on Laravel, Filament, and media architecture consistency.
---

# Laravel + Filament Spatie Media Library

This skill defines a strict, reusable standard for implementing image handling with Spatie Media Library in Laravel and Filament projects.

Apply this skill when:

- adding image support to a model
- refactoring inconsistent media handling
- creating or updating Filament upload fields
- creating or updating Filament image columns
- writing seeders that attach placeholder images
- standardizing image conversion naming
- adding image URLs to API resources
- reviewing whether a project follows a consistent media architecture

This skill is Laravel and Filament focused. Ignore frontend framework specifics.

Target areas usually include:

- Eloquent models
- Filament form schemas
- Filament table columns
- API resources
- Seeders
- support helpers related to image generation or placeholders

## Core Architecture

All images must be managed through Spatie Media Library.

Do not store image paths or filenames in model database columns when the image belongs in a media collection.

Use the `media` table as the source of truth for file management.

## Required Principles

- all images are managed through Spatie Media Library
- each media collection is single-file by default
- each media collection generates exactly 2 primary conversions unless a justified variation exists:
  - `optimized` = cropped or resized in original format
  - `web` = cropped or resized in WebP format
- all conversions must use `Fit::Crop`
- all conversions must use `nonQueued()`
- all uploads must use public visibility
- accepted upload input formats are JPEG and PNG only
- width and height values must be defined as model constants
- model constants are the single source of truth for dimensions
- Filament validation, helper text, conversions, and seeders must reference model constants
- hardcoded image dimensions in multiple layers are forbidden

## Single Source of Truth Rule

Image dimensions must be declared only in the model as public constants.

Every other layer must reference those constants.

Allowed examples:

- model conversions use `self::IMAGE_WIDTH`
- Filament validation uses `Model::IMAGE_WIDTH`
- Filament helper text uses `Model::IMAGE_WIDTH`
- seeders use `Model::IMAGE_WIDTH`

Do not duplicate width and height values across forms, seeders, resources, or helper classes.

If dimensions change, only the model constants should need updating.

## Model Layer Standard

### Required Interfaces and Traits

A model using this skill must implement and use the appropriate Spatie Media Library integration.

Typical requirements:

- implement `HasMedia`
- use `InteractsWithMedia`

### Constant Naming Rules

Define dimensions using public constants.

Use this naming pattern:

- `{COLLECTION}_WIDTH`
- `{COLLECTION}_HEIGHT`

Examples:

- `IMAGE_WIDTH`
- `IMAGE_HEIGHT`
- `HEADER_IMAGE_WIDTH`
- `HEADER_IMAGE_HEIGHT`
- `OG_IMAGE_WIDTH`
- `OG_IMAGE_HEIGHT`
- `LOGO_WIDTH`
- `LOGO_HEIGHT`
- `AUTHOR_IMAGE_WIDTH`
- `AUTHOR_IMAGE_HEIGHT`
- `THUMB_WIDTH`
- `THUMB_HEIGHT`

Use uppercase snake case.

### Single Collection Pattern

For a model with one image collection, use a single-file collection and two conversions.

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
            ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT)
            ->nonQueued();

        $this->addMediaConversion('web')
            ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT)
            ->format('webp')
            ->nonQueued();
    }
}
```

### Strict Rules for `optimized`

Do not call `->format()` on the `optimized` conversion.

Reason:

- `optimized` must preserve the original file format
- JPEG stays JPEG
- PNG stays PNG

This conversion is the resized or cropped fallback in the original format.

### Strict Rules for `web`

The `web` conversion must:

- use the same dimensions as `optimized`
- use `->format('webp')`
- use `Fit::Crop`
- use `nonQueued()`

`optimized` and `web` for the same image size must always use identical dimensions.

## Multiple Collection Pattern

If a model has multiple collections, bind conversions explicitly with `performOnCollections()`.

```php
public const HEADER_IMAGE_WIDTH = 1920;
public const HEADER_IMAGE_HEIGHT = 475;

public const OG_IMAGE_WIDTH = 1200;
public const OG_IMAGE_HEIGHT = 630;

public function registerMediaCollections(): void
{
    $this->addMediaCollection('header_image')->singleFile();
    $this->addMediaCollection('og_image')->singleFile();
}

public function registerMediaConversions(?Media $media = null): void
{
    $this->addMediaConversion('optimized')
        ->performOnCollections('header_image')
        ->fit(Fit::Crop, self::HEADER_IMAGE_WIDTH, self::HEADER_IMAGE_HEIGHT)
        ->nonQueued();

    $this->addMediaConversion('web')
        ->performOnCollections('header_image')
        ->fit(Fit::Crop, self::HEADER_IMAGE_WIDTH, self::HEADER_IMAGE_HEIGHT)
        ->format('webp')
        ->nonQueued();

    $this->addMediaConversion('og-optimized')
        ->performOnCollections('og_image')
        ->fit(Fit::Crop, self::OG_IMAGE_WIDTH, self::OG_IMAGE_HEIGHT)
        ->nonQueued();

    $this->addMediaConversion('og-web')
        ->performOnCollections('og_image')
        ->fit(Fit::Crop, self::OG_IMAGE_WIDTH, self::OG_IMAGE_HEIGHT)
        ->format('webp')
        ->nonQueued();
}
```

### Conversion Naming Rules

Use predictable names.

Default naming for a single collection and one size:

- `optimized`
- `web`

Naming for additional sizes or prefixed collections:

- `{prefix}-optimized`
- `{prefix}-web`

Examples:

- `thumb-optimized`
- `thumb`
- `og-optimized`
- `og-web`
- `favicon-optimized`
- `favicon-web`
- `footer-optimized`
- `footer-web`

### Naming Rule for Thumbnail Variants

When a smaller thumbnail version exists alongside the main image, use:

- `thumb-optimized`
- `thumb`

Keep the main size as:

- `optimized`
- `web`

This creates a consistent semantic structure:

- main original-format fallback
- main WebP
- thumbnail original-format fallback
- thumbnail WebP

## Multiple Size Pattern

If a model requires both a primary image size and a smaller thumbnail size, define separate constants and conversions.

```php
public const IMAGE_WIDTH = 830;
public const IMAGE_HEIGHT = 450;

public const THUMB_WIDTH = 400;
public const THUMB_HEIGHT = 220;

public function registerMediaConversions(?Media $media = null): void
{
    $this->addMediaConversion('thumb-optimized')
        ->fit(Fit::Crop, self::THUMB_WIDTH, self::THUMB_HEIGHT)
        ->nonQueued();

    $this->addMediaConversion('thumb')
        ->fit(Fit::Crop, self::THUMB_WIDTH, self::THUMB_HEIGHT)
        ->format('webp')
        ->nonQueued();

    $this->addMediaConversion('optimized')
        ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT)
        ->nonQueued();

    $this->addMediaConversion('web')
        ->fit(Fit::Crop, self::IMAGE_WIDTH, self::IMAGE_HEIGHT)
        ->format('webp')
        ->nonQueued();
}
```

## Filament Form Standard

Use `SpatieMediaLibraryFileUpload` for image uploads in Filament when the project uses Spatie Media Library.

### Required Upload Field Rules

A Filament image upload field must include:

- `->collection('...')`
- `->conversion('web')` for preview
- `->label('...')`
- `->helperText(...)` built from model constants
- `->rule(Rule::dimensions()...)` built from model constants
- `->image()`
- `->acceptedFileTypes(['image/jpeg', 'image/png'])`
- `->maxSize(...)`
- `->visibility('public')`
- `->openable()`

Use `columnSpanFull()` when the form layout makes a full-width upload field appropriate.

```php
use App\Models\Slider;
use Illuminate\Validation\Rule;
use Filament\Forms\Components\SpatieMediaLibraryFileUpload;

SpatieMediaLibraryFileUpload::make('image')
    ->collection('image')
    ->conversion('web')
    ->label('Image')
    ->helperText('Min. '.Slider::IMAGE_WIDTH.'x'.Slider::IMAGE_HEIGHT.' px | JPG, PNG | Max. 2 MB')
    ->rule(
        Rule::dimensions()
            ->minWidth(Slider::IMAGE_WIDTH)
            ->minHeight(Slider::IMAGE_HEIGHT)
    )
    ->image()
    ->acceptedFileTypes(['image/jpeg', 'image/png'])
    ->maxSize(2048)
    ->visibility('public')
    ->openable()
    ->columnSpanFull();
```

### Validation Rule Requirement

Dimension validation must reference model constants.

Do not hardcode dimensions in the Filament form if the model already defines them.

### Helper Text Requirement

Helper text must reflect the same constants used in validation and conversions.

Do not manually type dimensions that duplicate model constants.

### Public Visibility Requirement

Always set:

```php
->visibility('public')
```

Do not rely on package defaults.

Reason:

- media often needs to be visible in admin previews and public output
- default visibility assumptions can create broken image access

### Accepted File Types

Accept only:

- `image/jpeg`
- `image/png`

Do not allow SVG, GIF, AVIF, WebP, or any other format unless a project explicitly overrides this standard.

### Upload Limit Rule

Set a max file size appropriate to the collection.

Use a clear, intentional size in KB.

Common examples:

- `2048` for 2 MB
- `1024` for 1 MB

## Singleton Model Rule in Filament Pages

If the media field belongs to a singleton model managed through a Filament Page instead of a standard Resource record, explicitly bind the upload field to the singleton model instance.

Use this pattern:

```php
->model(fn () => Setting::firstOrCreate([]))
```

Apply this when a page edits a single global record, such as:

- settings
- about page
- site configuration
- global branding

```php
SpatieMediaLibraryFileUpload::make('logo')
    ->collection('logo')
    ->conversion('web')
    ->model(fn () => Setting::firstOrCreate([]))
    ->helperText('Min. '.Setting::LOGO_WIDTH.'x'.Setting::LOGO_HEIGHT.' px | JPG, PNG | Max. 2 MB')
    ->rule(
        Rule::dimensions()
            ->minWidth(Setting::LOGO_WIDTH)
            ->minHeight(Setting::LOGO_HEIGHT)
    )
    ->image()
    ->acceptedFileTypes(['image/jpeg', 'image/png'])
    ->maxSize(2048)
    ->visibility('public')
    ->openable();
```

## Filament Table Standard

Use `SpatieMediaLibraryImageColumn` when showing media in Filament tables.

Required configuration should usually include:

- `->collection('...')`
- `->conversion('web')`
- `->label('...')`

Optional configuration may include:

- `->imageSize(...)`
- `->circular()` for avatar-like images

```php
use Filament\Tables\Columns\SpatieMediaLibraryImageColumn;

SpatieMediaLibraryImageColumn::make('image')
    ->collection('image')
    ->conversion('web')
    ->label('Image')
    ->imageSize(40);
```

For avatar-like images:

```php
SpatieMediaLibraryImageColumn::make('author_image')
    ->collection('author_image')
    ->conversion('web')
    ->label('Author')
    ->imageSize(40)
    ->circular();
```

### Table Column Rule

Always use the correct collection name and conversion name.

Do not guess conversion names.

Keep table previews aligned with the actual conversion naming strategy of the model.

## Seeder Standard

Seeders attaching media must use model constants and must avoid duplication.

### Required Seeder Rules

- always check `hasMedia()` before adding media
- always use model constants for dimensions
- always attach the media to the intended collection
- seeders must be safe to run more than once

```php
use App\Models\Slider;
use App\Support\Helper;

if (! $slider->hasMedia('image')) {
    $path = Helper::downloadFakeImage(Slider::IMAGE_WIDTH, Slider::IMAGE_HEIGHT, 'Slider');

    if ($path) {
        $slider->addMedia($path)->toMediaCollection('image');
    }
}
```

### Multiple Collection Seeder Pattern

If a model has several image collections, use a loop with an explicit collection map.

```php
use App\Models\Setting;

$mediaCollections = [
    'logo'              => [Setting::LOGO_WIDTH, Setting::LOGO_HEIGHT, 'Logo'],
    'favicon'           => [Setting::FAVICON_WIDTH, Setting::FAVICON_HEIGHT, 'Favicon'],
    'logo_footer'       => [Setting::LOGO_FOOTER_WIDTH, Setting::LOGO_FOOTER_HEIGHT, 'Footer Logo'],
    'logo_footer_small' => [Setting::LOGO_FOOTER_SMALL_WIDTH, Setting::LOGO_FOOTER_SMALL_HEIGHT, 'Footer Small'],
];

foreach ($mediaCollections as $collection => [$width, $height, $text]) {
    if ($setting->hasMedia($collection)) {
        continue;
    }

    $path = Helper::downloadFakeImage($width, $height, $text);

    if ($path) {
        $setting->addMedia($path)->toMediaCollection($collection);
    }
}
```

### Seeder Safety Rule

Seeders must be idempotent with respect to media attachment.

Do not add the same collection media repeatedly if it already exists.

## Helper Utility Standard

If the project uses a helper for generating fake placeholder images during seeding, the helper should be generic and framework-safe.

Recommended signature:

```php
Helper::downloadFakeImage(int $width, int $height, ?string $text = null): string|bool
```

Expected behavior:

- creates a placeholder image locally
- uses GD or another local image library
- stores the file in a temporary directory
- returns the file path on success
- returns `false` on failure
- logs failures appropriately

### Helper Rule

Use a local placeholder generation strategy when possible.

Do not make seeders depend on unstable external image services if the same result can be achieved locally.

## API Resource Standard

When a model exposes images through API resources, return a structured set of URLs for each collection.

At minimum, include:

- original media URL
- optimized conversion URL
- web conversion URL

```php
public function toArray(Request $request): array
{
    return [
        'image' => $this->getFirstMediaUrl('image') ?: null,
        'image_optimized' => $this->getFirstMediaUrl('image', 'optimized') ?: null,
        'image_web' => $this->getFirstMediaUrl('image', 'web') ?: null,
    ];
}
```

### API Naming Rule

Use clear, predictable keys.

Preferred pattern:

- `{collection}`
- `{collection}_optimized`
- `{collection}_web`

Examples:

- `image`
- `image_optimized`
- `image_web`
- `logo`
- `logo_optimized`
- `logo_web`

Do not invent inconsistent response keys per model.

## Collection and Conversion Design Rules

### Default Collection Rule

Collections are single-file by default.

Use:

```php
->singleFile()
```

Do not create multi-file collections unless the project explicitly requires gallery-like behavior.

### Crop Rule

All conversions must use:

```php
Fit::Crop
```

Do not use:

- `Fit::Contain`
- `Fit::Max`
- other resize strategies

unless the project has a very explicit exception.

### Queue Rule

All conversions must use:

```php
->nonQueued()
```

Do not introduce queued conversions unless the project explicitly has a queue-based media pipeline.

### Dual Conversion Rule

Each collection must have dual conversions for the same size:

- original-format resized fallback
- WebP variant

This standard exists to support broad compatibility while still offering modern image delivery.

## New Media Field Implementation Checklist

When adding a new image field to a model, follow this order:

1. Define width and height constants in the model.
2. Add the media collection with `->singleFile()`.
3. Add conversions using constants.
4. Add the Filament upload field.
5. Add the Filament image table column if needed.
6. Update or create the seeder logic.
7. Update the API resource to expose image URLs.
8. Update project documentation or planning files if the repository uses them.

### Required Implementation Sequence

Step 1, model constants:

- `public const {COLLECTION}_WIDTH = ...;`
- `public const {COLLECTION}_HEIGHT = ...;`

Step 2, media collection:

```php
$this->addMediaCollection('collection_name')->singleFile();
```

Step 3, conversions:

- `optimized`
- `web`

or prefixed equivalents for multi-collection models.

Step 4, Filament upload field:

- collection
- conversion
- helper text from constants
- validation from constants
- JPEG or PNG restriction
- public visibility

Step 5, Filament table preview:

- use `SpatieMediaLibraryImageColumn` if the image should appear in listings

Step 6, seeder support:

- use `hasMedia()` and placeholder generation with model constants

Step 7, API resource support:

- expose original, optimized, and web URLs

## Before Implementing

Before modifying code, inspect the project for:

- existing Spatie Media Library usage
- current collection naming patterns
- current conversion naming patterns
- current file visibility settings
- whether models already define constants
- whether a helper already exists for placeholder generation
- whether the project uses singleton Filament pages
- whether the project already exposes media URLs via API resources

Do not introduce a parallel or conflicting media architecture.

If the project already follows a stronger established pattern, align with it unless the task is to standardize and refactor.

## Enforcement Rules

If the existing implementation is inconsistent, refactor it toward this standard.

Do not preserve broken or fragmented patterns just because they already exist.

Examples of inconsistency that should be corrected:

- dimensions hardcoded in forms but not in models
- missing `web` conversion
- missing `optimized` conversion
- file uploads without `visibility('public')`
- random conversion names
- mixed use of stored path columns and media collections for the same image concern
- repeated seeder media duplication
- missing `singleFile()` on collections that should only hold one image

## Anti-Patterns

Do not:

- store image paths in normal model columns for images that belong in media collections
- hardcode image dimensions in multiple layers
- omit model constants
- use `format()` on `optimized`
- use different dimensions for `optimized` and `web` for the same image size
- allow unsupported upload formats by default
- omit `visibility('public')`
- omit `nonQueued()`
- use multiple files in a collection without a clear requirement
- build ad hoc upload logic outside Spatie Media Library
- duplicate seed media attachments on every seeder run
- expose inconsistent API key names for media URLs
- use guessed conversion names in Filament tables or forms

## Expected Output

When applying this skill, the agent must:

1. inspect the current model and media structure
2. define or normalize model constants
3. define or normalize media collections
4. define or normalize conversions
5. update Filament upload components
6. update Filament image columns if relevant
7. update seeders
8. update API resources if the model is API-facing
9. preserve consistency in naming and architecture
10. summarize what changed and note any assumptions

## Final Rule

Consistency is mandatory.

For Laravel and Filament projects using Spatie Media Library:

- model constants are the single source of truth
- collections are single-file by default
- conversions are dual-format by default
- uploads are public
- conversions are synchronous
- cropping is fixed
- JPEG and PNG are the accepted input formats
- every layer must align with the same media contract
