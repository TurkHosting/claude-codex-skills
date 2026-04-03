# Global And Scoped Ordering

## Global Ordering

Use global ordering when records are ordered across the full table.

- On create: assign the next value.
- On update: shift only the affected range.
- On delete: close the gap by default.

## Scoped Ordering

Use scoped ordering when ordering is limited to a parent or scope such as:

- `category_id`
- `parent_id`
- `sub_category_id`
- `team_id`
- `project_id`

Rules:

- On create: assign the next value within the scope.
- On update: reorder only within the relevant scope.
- On scope change: remove from the old scope, close gaps if needed, then insert into the new scope.
- On delete: shift only records in the same scope.

## Shift Logic

When moving from `old_position` to `new_position`:

- if `new_position < old_position`, increment records between `new_position` and `old_position - 1`
- if `new_position > old_position`, decrement records between `old_position + 1` and `new_position`
