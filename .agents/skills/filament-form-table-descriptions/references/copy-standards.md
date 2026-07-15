# Copy Standards

How to write meaningful helperText, Section descriptions, table descriptions, and hints. Read this before writing any copy.

## The model: what / why / do

Every description must answer three questions for a non-technical panel administrator:

```
WHAT   — what this field/section/table represents, in plain business terms
WHY    — the purpose; what changes on the live site / customer experience when set
DO     — the concrete action or decision, plus any constraint (format, limit,
         immutability, recommended value)
```

A short hint does not need three sentences — often one sentence carries why + do together. The point is that none of the three is *missing*, not that each gets its own clause.

## Understand the feature first (do this before writing)

Copy is only meaningful if it reflects what the feature actually does. Before writing:

- Read the model and its casts, relationships, validation rules / form requests, observers, enums, and any constant the field uses.
- Trace how the value is consumed — where it surfaces on the storefront, in emails, in calculations, or in other admin screens.
- Only then write copy that answers what / why / do.

## Worked examples (before → after)

A `sort_order` field, auto-assigned by an observer and changed by drag-and-drop in the table:

- ❌ Lazy: "Sort order." — restates the label, tells the admin nothing.
- ✅ Meaningful: "Controls the position of this item in customer-facing lists. It is set automatically when you create the record; reorder any time by dragging rows in the table."

An `is_active` toggle:

- ❌ Lazy: "Whether it is active."
- ✅ Meaningful: "When turned off, the item is hidden from the storefront without being deleted."

A `meta_title` SEO field:

- ❌ Lazy: "Meta title."
- ✅ Meaningful: "Shown as the clickable title in search engine results; falls back to the normal name if left empty. Aim for under 60 characters."

## Rules

- Helper text explains the **consequence, constraint, or recommendation** — never merely restates the label.
- State hard constraints explicitly: immutability ("cannot be changed after creation"), validation limits, recommended image dimensions, SEO character counts.
- Frame from the **shop owner / admin's** perspective in plain business language — describe the storefront/customer effect, not the database column or internal mechanism.
- Keep helper text to 1–2 sentences. Section descriptions may run up to ~3 sentences since they cover a group.

## `helperText()` vs `hintIcon()`

- Default to `->helperText()` (text beneath the field) for guidance the user should read while filling the form.
- Use `->hintIcon('heroicon-m-question-mark-circle', tooltip: __('admin.{field}_hint'))` only for secondary detail that would clutter the layout if always visible.
- Never duplicate the same text in both surfaces.

```php
TextInput::make('password')
    ->label(__('admin.password'))
    ->helperText(__('admin.password_leave_blank_hint'))
    ->hintIcon('heroicon-m-question-mark-circle', tooltip: __('admin.password_hint'));
```

## Parameterized hints

When a hint references a dynamic value (image size, a constant), use `:placeholder` tokens and pass them via the second `__()` argument — never inline numbers.

```php
SpatieMediaLibraryFileUpload::make('images')
    ->helperText(__('admin.image_min_dimensions_hint', [
        'width' => Product::IMAGE_WIDTH,
        'height' => Product::IMAGE_HEIGHT,
    ]));
```

```php
// lang/en/admin.php
'image_min_dimensions_hint' => 'Upload an image at least :widthx:height px — smaller images are rejected.',
```

## Find a canonical example in your project

Before building a new form/table, mirror a sibling in the same project that already follows this standard. Search `app/Filament/**/Schemas/*Form.php` and `app/Filament/**/Tables/*Table.php` for the closest match to:

- a **Form** with Section descriptions + per-field hints,
- a **Form** with a parameterized image hint + a collapsible SEO section,
- a form field using the `hintIcon` tooltip variant,
- a **Table** with a description + empty-state heading/description,

and follow its shape and imports rather than writing from scratch.
