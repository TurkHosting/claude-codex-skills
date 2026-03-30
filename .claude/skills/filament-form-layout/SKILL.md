---
name: filament-form-layout
description: Enforce consistent FilamentPHP form layout standards for content, configuration, landing, and simple forms. Use when creating or refactoring Filament Resources or Page forms to ensure UI consistency, proper field grouping, and correct use of Grid, Section, and sidebar patterns.
---

# Filament Form Layout Skill

This skill defines a strict layout and structural standard for building FilamentPHP forms.

It MUST be applied when:

- Creating a new Filament Resource form
- Refactoring an existing form
- Standardizing inconsistent UI/UX across resources

Target paths:

- app/Filament/Resources/**/Schemas/*Form.php
- Filament Page form schemas

---

# Core Principle

Before building or modifying any form, you MUST classify the form type.

## Required Decision Step

Determine which of the following types the form belongs to:

1. Content-driven form
2. Configuration / metadata form
3. Landing / complex content form
4. Simple form

Do NOT start coding before making this classification.

---

# 1. Content-Driven Form Standard

## Applies to

- Blog
- Page
- Articles
- Knowledge base / documentation
- Any content with SEO + publishing logic

## Layout Rules

Use a 3-column layout with sidebar:

LEFT (main content area):
- title
- slug
- body/content
- images

RIGHT (sidebar):
- status
- published_at
- author/user
- categories
- tags
- toggles (visibility, editor mode, etc.)

SEO:
- must NOT be primary content
- place AFTER main content OR in collapsible section

## Required Structure

Use Grid + Section layout.

Example:

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

## Strict Rules

- NEVER mix status/relations into main content
- SEO must be secondary (collapsed if possible)
- Sidebar is REQUIRED unless form is extremely small
- Main content must always be visually dominant

---

# 2. Configuration / Metadata Form Standard

## Applies to

- Category
- Tag
- Package
- Settings
- Menus, links, small data structures

## Layout Decision

You MUST decide:

### Use sidebar IF:

- toggle fields exist (is_active, featured, etc.)
- ordering exists (order)
- multiple relations exist

### Do NOT use sidebar IF:

- form is small (3–6 fields)
- linear input flow is clearer

## Structure Options

### Small form

```php
Section::make('General')
    ->components([
        // fields
    ]);
```

### Medium form

Use multiple Sections stacked vertically.

### With sidebar

Only when justified. Do NOT force it.

---

# 3. Landing / Complex Content Form Standard

## Applies to

- Sliders
- Landing pages
- Campaign pages
- Pricing or marketing blocks

## Layout Rules

Use Grid + sidebar (NOT tabs).

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

## Important Rule

If the form can be managed in a single screen:

- DO NOT use Tabs
- Use Sections with hierarchy

## Collapsible Usage

Use for:

- rarely edited fields
- secondary features
- optional configurations

---

# 4. Simple Form Standard

## Applies to

- small CRUD forms
- single-purpose forms

## Rules

- single Section
- no Grid unless necessary
- no sidebar
- no collapsible unless absolutely needed

Example:

```php
Section::make('General')
    ->components([
        TextInput::make('name')->required(),
        TextInput::make('slug'),
        TextInput::make('order')->numeric(),
    ]);
```

## Strict Rule

DO NOT over-engineer simple forms.

---

# Component Usage Rules

## Schema Convention (MANDATORY)

Always use:

```php
$schema->columns(...)->components([...]);
```

Do NOT switch to `->schema([...])` unless the entire project uses it.

Consistency is more important than framework examples.

---

## Grid Usage

Grid is ONLY for layout separation:

- main content vs sidebar
- responsive columns

DO NOT use Grid for styling only.

---

## Section Usage

Use Section when:

- logical grouping exists
- UI clarity improves
- title adds meaning

Avoid meaningless Sections.

---

## Collapsible Usage

Allowed for:

- SEO
- advanced settings
- secondary media

Rules:

- critical fields must NEVER be collapsed
- SEO SHOULD be collapsed by default

---

## File Upload Components

Default to:

- FileUpload
- RichEditor attachments

Do NOT introduce new upload systems unless already used in project.

---

# Layout Decision Matrix

| Form Type      | Layout                |
| -------------- | --------------------- |
| Content + SEO  | Grid + sidebar        |
| Medium content | 2-column OR sidebar   |
| Simple form    | Section only          |
| Landing form   | Grid + sidebar        |
| Config form    | depends on complexity |

---

# Mandatory Design Rules

- Do NOT make small forms complex
- Do NOT mix content and metadata
- Do NOT prioritize SEO visually
- Always isolate status, order, relations
- Maintain consistency across all resources
- Follow existing naming and field patterns

---

# Implementation Checklist

Before finishing a form:

- Is the form type correctly classified?
- Is main content clearly dominant?
- Are status/order/relations separated?
- Is SEO secondary?
- Is layout consistent with other resources?
- Is this over-engineered?

If any answer is incorrect, refactor.

---

# Anti-Patterns (DO NOT DO)

- mixing status fields into content sections
- using sidebar for tiny forms
- using tabs for simple flows
- collapsing important fields
- inconsistent schema syntax
- introducing new layout patterns per resource

---

# Expected Output

When applying this skill, the agent must:

1. Analyze the resource type
2. Choose correct layout pattern
3. Apply proper Grid/Section structure
4. Place fields in correct regions
5. Keep consistency with the project
6. Avoid over-engineering
7. Produce clean, readable schema

---

# Final Rule

Consistency over creativity.

Follow the system. Do not invent new layouts.
