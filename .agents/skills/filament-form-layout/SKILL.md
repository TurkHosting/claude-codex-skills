---
name: filament-form-layout
description: Enforce consistent FilamentPHP form layout standards for content, configuration, landing, and simple forms. Use when creating, refactoring, or reviewing Filament Resource or Page forms to ensure UI consistency, correct field grouping, and proper use of Grid, Section, and sidebar patterns.
---

# Filament Form Layout

Apply this skill when creating, refactoring, or reviewing Filament forms that need a consistent structure.

Target paths:

- `app/Filament/Resources/**/Schemas/*Form.php`
- Filament Page form schemas

## Activation

Use this skill when working on:

- content-heavy resource forms
- metadata or configuration forms
- landing or marketing forms
- small CRUD forms that are drifting into inconsistent layouts

## Core Rules

- Inspect existing form patterns before changing layout decisions.
- Classify the form before writing code:
  - content-driven
  - configuration or metadata
  - landing or complex content
  - simple
- Do not introduce a new layout system if the project already has a stronger convention.
- Keep `SKILL.md` short and use the references below for the actual layout standards.

## References

- `references/content-driven-form.md`
- `references/configuration-form.md`
- `references/landing-form.md`
- `references/simple-form.md`
- `references/component-rules.md`

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
