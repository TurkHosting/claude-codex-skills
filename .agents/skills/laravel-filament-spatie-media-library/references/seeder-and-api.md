# Seeder And API Rules

## Seeder Rules

- Use model constants for dimensions.
- Check `hasMedia()` before attaching files.
- Attach to the intended collection only.
- Keep seeders idempotent.
- Prefer local placeholder helpers over unstable external image services.

## API Resource Rules

If the model is API-facing, expose predictable keys:

- `{collection}`
- `{collection}_optimized`
- `{collection}_web`

Do not invent a different naming scheme per model.
