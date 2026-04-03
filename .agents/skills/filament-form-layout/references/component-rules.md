# Component Rules

## Schema Convention

Prefer:

```php
$schema->columns(...)->components([...]);
```

Do not switch to `->schema([...])` unless the project already uses it consistently.

## Grid Rules

- Use `Grid` for layout separation, not styling only.
- Typical reasons: main content vs sidebar, responsive columns.

## Section Rules

- Use `Section` to group meaningful domains of fields.
- Do not wrap every small field group in unnecessary sections.

## Layout Safety

- Do not mix publishing and relation controls into main content sections.
- Do not make small forms look like complex editorial screens.
- Prefer consistency with sibling resources over framework-demo aesthetics.
