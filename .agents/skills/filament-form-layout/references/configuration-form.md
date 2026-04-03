# Configuration Or Metadata Form

Apply this layout to category, tag, settings, package, menu, and other small data structures.

## Rules

- Prefer a single section for small forms.
- Use stacked sections for medium forms.
- Add a sidebar only when toggles, ordering, or multiple relations make it clearly useful.
- Do not force a sidebar for a 3 to 6 field form.

## Example

```php
Section::make('General')
    ->components([
        // fields
    ]);
```
