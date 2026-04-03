# Filament Integration

## Form Rule

Hide the ordering field on create:

```php
TextInput::make('order')
    ->numeric()
    ->hiddenOn('create')
```

Expected behavior:

- create: observer assigns the value
- edit: manual reordering may be allowed if the project wants it

## Table Rule

Use:

```php
->reorderable('order')
```

Replace `'order'` with the actual ordering column.

Do not build a second custom reordering system in Filament that bypasses the observer.
