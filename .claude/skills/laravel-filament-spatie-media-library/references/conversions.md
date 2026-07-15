# Conversion Rules

## Default Pattern

For each image size, prefer:

- `optimized`
- `web`

For extra collections or sizes, use predictable names such as:

- `thumb-optimized`
- `thumb`
- `og-optimized`
- `og-web`

## Rules

- Use `Fit::Crop`.
- Do not call `->format()` on `optimized`.
- Always call `->format('webp')` on `web`.
- Keep `optimized` and `web` dimensions identical for the same size.
- Use queued conversions by default (do not call `nonQueued()`). The project has `queue_conversions_by_default: true` in `config/media-library.php`.
- **Exception — Seeders:** In seeders, temporarily disable queued conversions before attaching media so they run synchronously without a queue worker:
  ```php
  config(['media-library.queue_conversions_by_default' => false]);
  $model->addMedia($path)->toMediaCollection('logo');
  ```
  Note: Spatie's `FileAdder` does not expose `nonQueued()` — that method only exists on `Conversion` definitions inside `registerMediaConversions()`. Use the config override in seeder scope instead.
- Use `performOnCollections()` when multiple collections exist.
