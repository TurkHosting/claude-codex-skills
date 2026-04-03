# Simple Form

Apply this layout to small CRUD or single-purpose forms.

## Rules

- Use a single `Section`.
- Do not use `Grid` unless it solves a real layout problem.
- Do not use a sidebar.
- Do not introduce collapsible sections unless they are clearly justified.

## Example

```php
Section::make('General')
    ->components([
        TextInput::make('name')->required(),
        TextInput::make('slug'),
        TextInput::make('order')->numeric(),
    ]);
```
