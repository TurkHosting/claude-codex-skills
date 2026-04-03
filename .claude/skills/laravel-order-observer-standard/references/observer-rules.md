# Observer Rules

## Auto Assignment

- Assign the ordering value automatically on create if it is empty.
- Preserve manual overrides only if the project explicitly allows them.

## Update Optimization

Prefer:

```php
$model->isClean('order')
```

Replace `'order'` with the chosen ordering column.

## Original Value Access

Prefer:

```php
$model->getOriginal('order')
```

Use the original value when calculating shifts.

## Safety

- Use `saveQuietly()` when updating sibling records inside the observer.
- Do not assume `max(column) + 1` is concurrency-safe by itself.
- Use transactions, constraints, or retry logic when stronger guarantees are needed.
