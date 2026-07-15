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
- **Queueing is a per-project decision, not a fixed rule.** If the project runs a queue worker, prefer queued conversions and do not call `nonQueued()`; if it does not (or wants synchronous generation), call `->nonQueued()` on the conversions, or set `queue_conversions_by_default => false` in `config/media-library.php`. Match whatever the project's queue setup actually is.
- **Seeders (regardless of the runtime choice):** generate images synchronously — no worker runs during seeding. Either call `->nonQueued()` on the conversions, or override the config in seeder scope before attaching:
  ```php
  config(['media-library.queue_conversions_by_default' => false]);
  $model->addMedia($path)->toMediaCollection('logo');
  ```
  Note: Spatie's `FileAdder` does not expose `nonQueued()` — that method only exists on `Conversion` definitions inside `registerMediaConversions()`. In seeder scope, use the config override.
- Use `performOnCollections()` when multiple collections exist.
