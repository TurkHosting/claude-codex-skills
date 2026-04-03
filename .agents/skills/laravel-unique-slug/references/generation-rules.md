# Generation Rules

## Base Behavior

- Generate slugs with `Str::slug()`.
- If the result is empty, use a safe fallback such as `item`.
- First collision keeps the base slug.
- Later collisions append numeric suffixes: `-1`, `-2`, `-3`.

## Conflict Detection

Treat these as valid sequence members:

- `my-post`
- `my-post-1`
- `my-post-25`

Ignore:

- `my-post-old`
- `my-post-copy`
- `my-post-final`

## Query Rules

- Query the base slug and suffixed variants.
- Exclude the current model during updates.
- Include trashed rows when the project reserves slugs across soft deletes.
- Parse suffixes with a strict numeric regex.
- Do not rely on naive string splitting.

## Stability

- Do not regenerate slugs on update unless the project explicitly opts in.
- Do not overwrite a manually provided slug unless the project explicitly requires normalization.
