# Edge Cases

- The first record should receive `1`.
- Manual override should be preserved only when explicitly allowed.
- Scope changes must be treated as removal from one scope and insertion into another.
- If the project allows ordering gaps, align with that convention instead of forcing reindexing.

## Anti-Patterns

- mixing `position` and `order`
- assigning ordering in controllers
- recalculating every row on every update
- bypassing observer logic
- ignoring scope boundaries
