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
- Use `nonQueued()` unless the project explicitly needs queued conversions.
- Use `performOnCollections()` when multiple collections exist.
