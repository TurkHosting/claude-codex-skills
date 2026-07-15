# Filament Syntax & Imports

Form and table layers use different namespaces and methods. Adding `->helperText()` / `->description()` / `->emptyStateHeading()` without the correct imports is a common cause of build/runtime errors. Confirm before writing.

## Standard shape

```php
// Form (app/Filament/Resources/**/Schemas/*Form.php)
Section::make(__('admin.currency_identity'))
    ->description(__('admin.currency_identity_description'))
    ->columns(2)
    ->components([
        Select::make('code')
            ->label(__('admin.code'))
            ->helperText(__('admin.currency_code_hint'))
            ->required(),
    ]);

// Table (app/Filament/Resources/**/Tables/*Table.php)
$table
    ->description(__('admin.currencies_page_description'))
    ->emptyStateHeading(__('admin.currencies_empty_heading'))
    ->emptyStateDescription(__('admin.currencies_empty_description'));
```

## Which method belongs on which layer

| Method | Belongs on | Layer |
| --- | --- | --- |
| `->helperText()` | form field component | form |
| `->hintIcon()` | form field component | form |
| `->description()` | `Section` | form layout |
| `->description()` | `$table` | table |
| `->emptyStateHeading()` / `->emptyStateDescription()` | `$table` | table |
| `->label()` | form field **and** table column | both |

They are not interchangeable across layers — a table has no `helperText()`, a form field has no `emptyStateHeading()`.

## Namespaces — never copy an import across layers

```
Filament\Schemas\Components\            → Section, Grid, Fieldset, Tabs, Wizard
Filament\Forms\Components\              → TextInput, Select, Toggle, Textarea, RichEditor, …
Filament\Tables\Columns\               → TextColumn, ToggleColumn, IconColumn, …
Filament\Schemas\Components\Utilities\  → Get, Set
```

## Verify before writing

- **If the laravel-boost MCP server is active, use its `search-docs` tool** to confirm the exact method name and signature against the installed Filament version. Do not rely on memory.
- **Mirror the imports already present in a sibling `*Form.php` / `*Table.php`** rather than guessing.
