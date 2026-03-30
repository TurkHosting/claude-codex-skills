---
name: filament-form-layout
description: Enforce consistent FilamentPHP form layout standards for content, configuration, landing, and simple forms. Use when creating, refactoring, or reviewing Filament Resource or Page forms to ensure UI consistency, correct field grouping, and proper use of Grid, Section, and sidebar patterns.
---

# Filament Form Layout Skill

Apply this skill to FilamentPHP forms that need consistent structure, predictable grouping, and stable layout decisions.

Target paths:

- `app/Filament/Resources/**/Schemas/*Form.php`
- Filament Page form schemas

## When to Activate

Activate this skill when:

- creating a new Filament Resource form
- refactoring an existing form
- reviewing form layout consistency
- standardizing layout patterns across resources

## Before Implementing

Inspect the project for:

- existing layout patterns
- existing form conventions
- existing `Grid` and `Section` usage
- consistency across related resources

Do NOT introduce a conflicting layout system if the project already has a stronger established convention.

## Core Principle

Classify the form type before writing code.

## Required Decision Step

Decide whether the form is:

1. a content-driven form
2. a configuration or metadata form
3. a landing or complex content form
4. a simple form

Do NOT start coding before making this classification.

## 1. Content-Driven Form Standard

Apply this standard to:

- blog forms
- page forms
- article forms
- knowledge base or documentation forms
- any content form with SEO and publishing logic

### Layout Rules

Use a 3-column layout with a sidebar.

Place the following in the LEFT main content area:

- title
- slug
- body or content
- images

Place the following in the RIGHT sidebar:

- status
- `published_at`
- author or user
- categories
- tags
- toggles such as visibility or editor mode

Treat SEO as secondary content:

- do NOT place it before primary content
- place it after main content or inside a collapsible section

### Required Structure

Use `Grid` and `Section`.

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

### Strict Rules

- Do NOT mix status or relations into the main content area.
- Keep SEO secondary and collapsed when possible.
- Use a sidebar unless the form is genuinely too small to justify one.
- Keep the main content area visually dominant.

## 2. Configuration or Metadata Form Standard

Apply this standard to:

- category forms
- tag forms
- package forms
- settings forms
- menus, links, and other small data structures

### Layout Decision

Decide whether the form requires a sidebar based on:

- whether toggle fields exist, such as `is_active` or `featured`
- whether ordering fields exist, such as `order`
- whether multiple relations exist

Do NOT use a sidebar when:

- the form is small, roughly 3 to 6 fields
- a linear input flow is clearer

### Structure Options

Use a single section for small forms:

```php
Section::make('General')
    ->components([
        // fields
    ]);
```

Use multiple stacked sections for medium forms.

Add a sidebar only when the form complexity clearly justifies it. Do NOT force a sidebar.

## 3. Landing or Complex Content Form Standard

Apply this standard to:

- slider forms
- landing page forms
- campaign page forms
- pricing or marketing block forms

### Layout Rules

Use `Grid` plus sidebar. Do NOT use tabs for a single-screen workflow.

Place the following in the LEFT content area:

- image
- title
- content
- pricing
- CTA
- features

Place the following in the RIGHT sidebar:

- order
- animation settings
- visibility toggles

### Collapsible Usage

Use collapsible sections for:

- rarely edited fields
- secondary features
- optional configurations

If the form can be managed on one screen, use sections with hierarchy instead of tabs.

## 4. Simple Form Standard

Apply this standard to:

- small CRUD forms
- single-purpose forms

### Rules

- use a single `Section`
- do NOT use `Grid` unless it is necessary
- do NOT use a sidebar
- do NOT use collapsible sections unless absolutely needed

```php
Section::make('General')
    ->components([
        TextInput::make('name')->required(),
        TextInput::make('slug'),
        TextInput::make('order')->numeric(),
    ]);
```

Do NOT over-engineer simple forms.

## Component Usage Rules

### Schema Convention

Use:

```php
$schema->columns(...)->components([...]);
```

Do NOT switch to `->schema([...])` unless the entire project already uses that style.

Prioritize consistency over framework example parity.

### Grid Usage

Use `Grid` only for layout separation:

- main content vs sidebar
- responsive columns

Do NOT use `Grid` for styling only.

### Section Usage

Use `Section` only when:

- logical grouping exists
- UI clarity improves
- the section title adds meaning

Avoid meaningless sections.

### Collapsible Usage

Use collapsible sections for:

- SEO
- advanced settings
- secondary media

Never collapse critical fields. Collapse SEO by default when possible.

### File Upload Components

Default to:

- `FileUpload`
- `RichEditor` attachments

Do NOT introduce a new upload system unless the project already uses it.

## Layout Decision Matrix

| Form Type | Layout |
| --- | --- |
| Content + SEO | Grid + sidebar |
| Medium content | 2-column or sidebar |
| Simple form | Section only |
| Landing form | Grid + sidebar |
| Config form | depends on complexity |

## Mandatory Design Rules

- Do NOT make small forms complex.
- Do NOT mix content and metadata.
- Do NOT prioritize SEO visually.
- Always isolate status, order, and relations.
- Maintain consistency across related resources.
- Follow existing naming and field patterns.

## Anti-Patterns

Do NOT:

- mix status fields into content sections
- use a sidebar for tiny forms
- use tabs for simple flows
- collapse important fields
- introduce inconsistent schema syntax
- invent a new layout pattern per resource

## Enforcement Rule

If the existing implementation does NOT follow this standard, refactor it unless the project already has a stronger established convention.

Do NOT preserve incorrect layout patterns only because they already exist.

## Agent Output Requirements

The agent MUST:

- classify the form type before writing code
- choose one layout pattern intentionally
- keep content, metadata, and SEO in separate regions
- preserve project consistency
- avoid over-engineering
- produce a clean, readable schema

## Final Check

Before finishing, confirm:

- the form type is correctly classified
- the main content is clearly dominant
- status, order, and relations are separated
- SEO is secondary
- the layout matches adjacent resources
- the result is not over-engineered

If any item fails, refactor.

## Final Rule

Prefer consistency over creativity.

Follow the system. Do not invent new layouts.
