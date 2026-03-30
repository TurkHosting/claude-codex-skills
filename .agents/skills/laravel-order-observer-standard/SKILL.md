---
name: laravel-order-observer-standard
description: Enforce consistent ordering (position or order) management using Laravel Observers and Filament integration. Use when implementing sortable models, automatic ordering, scoped ordering, or drag-and-drop reordering.
---

# Laravel Order / Position Observer Standard

Apply this skill when a Laravel project needs a consistent, automatic ordering system for Eloquent models using Observers.

## When to Activate

Activate this skill when:

- implementing sortable records
- managing `position` or `order` columns
- enabling drag and drop ordering in Filament
- refactoring inconsistent ordering logic
- working with parent-child or scope-based ordered data

## Before Implementing

Inspect the project for:

- the existing ordering column name
- whether the model uses global ordering or scoped ordering
- whether an observer already exists
- whether related models already use `position` or `order`
- whether Filament tables already use `->reorderable(...)`
- whether forms expose the ordering field
- whether the scope key can change during updates

Do not introduce conflicting ordering logic if the project already has a consistent ordering system.

## Core Rule

Use only one ordering column across the implementation:

- `position`
- or `order`

Never mix both in the same project.

If the project already uses one, continue with it.

If the project is new, prefer `position`.

Keep this naming consistent across:

- database columns
- model attributes
- observers
- Filament forms
- Filament tables

## Observer Registration Rule

Register observers with the PHP attribute:

```php
use Illuminate\Database\Eloquent\Attributes\ObservedBy;

#[ObservedBy(ModelObserver::class)]
class Model extends Eloquent
{
}
```

Do not use service providers or manual boot registration for this pattern.

## Ordering Patterns

### Global Ordering

Use global ordering when records are ordered across the entire table.

On create:

- assign the next value from the current max plus one

On update:

- skip work if the ordering column did not change
- shift only the affected records

On delete:

- shift records below the deleted item up by one by default

### Scoped Ordering

Use scoped ordering when records are ordered only within a parent or scope group.

Examples of scope keys:

- `category_id`
- `parent_id`
- `sub_category_id`
- `team_id`
- `project_id`

On create:

- assign the next value within the current scope

On update:

- reorder only within the active scope
- if the scope key changes, remove the record from the old scope ordering and reinsert it into the new scope ordering

On delete:

- shift only records within the same scope by default

## Shift Logic Rule

When moving a record from `old_position` to `new_position`:

- if `new_position < old_position`, increment the ordering value of records between `new_position` and `old_position - 1`
- if `new_position > old_position`, decrement the ordering value of records between `old_position + 1` and `new_position`

Apply the same algorithm within the active scope for scoped ordering models.

Do not leave the affected range ambiguous.

## Observer Rules

### Auto Assignment

On create:

- assign the ordering value automatically if it is empty
- preserve a manually provided value only if the project explicitly allows manual override

### Update Optimization

Use:

```php
$model->isClean('order')
```

Replace `'order'` with the project's chosen ordering column.

Skip unnecessary work when the ordering value did not change.

### Original Value Access

Use:

```php
$model->getOriginal('order')
```

Replace `'order'` with the project's chosen ordering column.

Use the original value when calculating how other rows must shift.

## Concurrency Rule

Assume concurrent writes.

Do not assume `max(column) + 1` is safe by itself under concurrency.

Use database constraints, transactions, or retry logic when the project requires stronger protection against duplicate ordering values.

## Safe Update Rule

When updating other records inside an observer:

- use `saveQuietly()`

This is mandatory to prevent recursive observer loops.

## Deletion Rule

After delete:

- shift remaining items up and close gaps by default

If the project explicitly allows ordering gaps, align with that convention instead of forcing reindexing.

## Parent or Scope Change Rule

If the parent or scope key changes:

- remove the record from the old scope ordering
- close the gap in the old scope if the project uses strict ordering
- insert the record into the new scope ordering at the correct position
- shift affected records in the new scope as needed

Do not treat scope changes like a simple in-place reorder.

## Filament Integration

### Form Rule

Hide the ordering field on create.

```php
TextInput::make('order')
    ->numeric()
    ->hiddenOn('create')
```

Replace `'order'` with the actual project column when needed.

Expected behavior:

- create: the observer assigns the value
- edit: the user may manually change the value if the project allows it

### Table Rule

If the table supports drag and drop ordering, use:

```php
->reorderable('order')
```

Replace `'order'` with the actual ordering column.

Do not implement a separate custom ordering system in Filament that conflicts with the observer pattern.

## Consistency Rules

- keep ordering logic in the observer
- do not manage ordering in controllers
- do not duplicate ordering logic in Filament actions or form handlers
- centralize all reorder behavior in one observer-driven system

## Edge Cases

- the first record must receive `1`
- manual override may be preserved only if the project explicitly allows it
- if the parent or scope key changes, reposition the record correctly inside the new scope

## Enforcement Rule

If ordering logic is inconsistent, duplicated, or broken:

- refactor it
- do not preserve broken logic just because it already exists

If the project already has a stronger established ordering convention, align with it instead of creating a parallel system.

## Anti-Patterns

Do not:

- mix `position` and `order`
- assign ordering values in controllers
- recalculate every row on every update
- bypass observer logic
- update related rows without `saveQuietly()`
- hardcode a specific scope key such as `parent_id` in a generic pattern
- ignore scope boundaries in scoped ordering models

## Agent Output Requirements

The agent must:

1. detect the ordering column name
2. detect whether ordering is global or scoped
3. detect the scope key when scoped ordering exists
4. implement the correct observer pattern
5. implement explicit shift logic for create, update, delete, and scope-change behavior
6. integrate the model with Filament forms and tables
7. prevent recursive observer loops
8. preserve a consistent ordering sequence

## Final Rule

Ordering must be:

- automatic
- consistent
- centralized in observers
- safe from recursion
- explicit in its shift logic
- compatible with Filament reordering
