---
name: filament-form-layout
description: Enforce consistent FilamentPHP form layout standards for content, configuration, landing, and simple forms. Use when creating or refactoring Filament Resources or Page forms to ensure UI consistency, proper field grouping, and correct use of Grid, Section, and sidebar patterns.
---

# Filament Form Layout

Apply a strict layout and structural standard when building FilamentPHP forms.

Use this skill when:

- creating a new Filament Resource form
- refactoring an existing form
- standardizing inconsistent UI or UX across resources

Target paths:

- `app/Filament/Resources/**/Schemas/*Form.php`
- Filament Page form schemas

## Core principle

Before building or modifying any form, classify the form type.

## Required decision step

Determine which of the following types the form belongs to:

1. Content-driven form
2. Configuration or metadata form
3. Landing or complex content form
4. Simple form

Do not start coding before making this classification.

## 1. Content-driven form standard

### Applies to

- Blog
- Page
- Articles
- Knowledge base or documentation
- Any content with SEO and publishing logic

### Layout rules

Use a 3-column layout with a sidebar.

LEFT, main content area:

- title
- slug
- body or content
- images

RIGHT, sidebar:

- status
- published_at
- author or user
- categories
- tags
- toggles such as visibility or editor mode

SEO:

- must not be primary content
- place it after main content or in a collapsible section

### Required structure

Use `Grid` plus `Section`.

```php
$schema
    ->columns(3)
    ->components([
        Grid::make(1)
            ->columnSpan(['default' => 3, 'lg' => 2])
            ->schema([
                Section::make('Content')
                    ->columns(2)
                    ->components([
                        // title, slug, body, image
                    ]),

                Section::make('SEO')
                    ->collapsible()
                    ->collapsed()
                    ->components([
                        // meta fields
                    ]),
            ]),

        Grid::make(1)
            ->columnSpan(['default' => 3, 'lg' => 1])
            ->schema([
                Section::make('Publishing')
                    ->components([
                        // status, published_at
                    ]),

                Section::make('Relations')
                    ->components([
                        // category, tags, user
                    ]),
            ]),
    ]);
```

### Strict rules

- never mix status or relations into main content
- SEO must be secondary and should be collapsed if possible
- the sidebar is required unless the form is extremely small
- main content must stay visually dominant

## 2. Configuration or metadata form standard

### Applies to

- Category
- Tag
- Package
- Settings
- Menus, links, and other small data structures

### Layout decision

You must decide:

Use a sidebar if:

- toggle fields exist, such as `is_active` or `featured`
- ordering exists, such as `order`
- multiple relations exist

Do not use a sidebar if:

- the form is small, about 3 to 6 fields
- a linear input flow is clearer

### Structure options

Small form:

```php
Section::make('General')
    ->components([
        // fields
    ]);
```

Medium form:

- use multiple `Section` blocks stacked vertically

With sidebar:

- use it only when justified
- do not force it

## 3. Landing or complex content form standard

### Applies to

- Sliders
- Landing pages
- Campaign pages
- Pricing or marketing blocks

### Layout rules

Use `Grid` plus sidebar, not tabs.

LEFT:

- image
- title
- content
- pricing
- CTA
- features

RIGHT:

- order
- animation settings
- visibility toggles

### Important rule

If the form can be managed in a single screen:

- do not use tabs
- use sections with hierarchy

### Collapsible usage

Use collapsible sections for:

- rarely edited fields
- secondary features
- optional configurations

## 4. Simple form standard

### Applies to

- small CRUD forms
- single-purpose forms

### Rules

- use a single `Section`
- do not use `Grid` unless necessary
- do not use a sidebar
- do not use collapsible sections unless absolutely needed

```php
Section::make('General')
    ->components([
        TextInput::make('name')->required(),
        TextInput::make('slug'),
        TextInput::make('order')->numeric(),
    ]);
```

### Strict rule

Do not over-engineer simple forms.

## Component usage rules

### Schema convention

Always use:

```php
$schema->columns(...)->components([...]);
```

Do not switch to `->schema([...])` unless the entire project already uses it.

Consistency matters more than framework examples.

### Grid usage

Use `Grid` only for layout separation:

- main content vs sidebar
- responsive columns

Do not use `Grid` only for styling.

### Section usage

Use `Section` when:

- logical grouping exists
- UI clarity improves
- the title adds meaning

Avoid meaningless sections.

### Collapsible usage

Allowed for:

- SEO
- advanced settings
- secondary media

Rules:

- critical fields must never be collapsed
- SEO should be collapsed by default

### File upload components

Default to:

- `FileUpload`
- `RichEditor` attachments

Do not introduce new upload systems unless the project already uses them.

## Layout decision matrix

| Form Type | Layout |
| --- | --- |
| Content + SEO | Grid + sidebar |
| Medium content | 2-column or sidebar |
| Simple form | Section only |
| Landing form | Grid + sidebar |
| Config form | depends on complexity |

## Mandatory design rules

- do not make small forms complex
- do not mix content and metadata
- do not prioritize SEO visually
- always isolate status, order, and relations
- maintain consistency across all resources
- follow existing naming and field patterns

## Implementation checklist

Before finishing a form, verify:

- the form type is correctly classified
- main content is clearly dominant
- status, order, and relations are separated
- SEO is secondary
- layout is consistent with other resources
- the implementation is not over-engineered

If any answer is no, refactor.

## Anti-patterns

Do not:

- mix status fields into content sections
- use a sidebar for tiny forms
- use tabs for simple flows
- collapse important fields
- introduce inconsistent schema syntax
- invent new layout patterns per resource

## Expected output

When applying this skill:

1. Analyze the resource type.
2. Choose the correct layout pattern.
3. Apply the proper `Grid` and `Section` structure.
4. Place fields in the correct regions.
5. Keep consistency with the project.
6. Avoid over-engineering.
7. Produce a clean, readable schema.

## Final rule

Consistency over creativity.

Follow the system. Do not invent new layouts.
