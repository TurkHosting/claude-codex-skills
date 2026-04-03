# Testing Requirements

Cover at least:

1. base slug generation from title
2. `-1` generation for the second duplicate
3. next suffix generation when multiple duplicates exist
4. ignoring non-numeric suffix variants
5. excluding the current record during update
6. respecting soft-deleted rows when slug reservation is enabled
7. preserving a manual slug
8. no regeneration on update when regeneration is disabled
9. regeneration on update when regeneration is enabled
10. fallback behavior when `Str::slug()` returns an empty string

If uniqueness is scoped, add tests for the chosen scope.
If route model binding changes, add routing tests too.
