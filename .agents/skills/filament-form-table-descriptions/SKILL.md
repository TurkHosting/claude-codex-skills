---
name: filament-form-table-descriptions
description: Enforce consistent contextual copy (helperText, Section description, table description, empty states) across every Filament form and table in the admin panel. Route copy through the project's translation layer; in multi-language projects keep every configured locale in parity. Use when creating, refactoring, or reviewing any Filament Resource, Page, or Relation Manager form schema or table.
---

# Filament Form & Table Descriptions

Every admin-facing form and table must guide the user with contextual copy. A field or table without explanatory text is considered **incomplete** — this is a first-class UX requirement, not optional polish.

Target paths:

- `app/Filament/Resources/**/Schemas/*Form.php`
- `app/Filament/Resources/**/Tables/*Table.php`
- `app/Filament/Pages/*.php` (form schemas)
- Relation Manager form/table schemas

## Activation

Use this skill when working on:

- creating a new Filament form, table, Page, or Relation Manager
- refactoring or reviewing an existing form/table
- adding or editing fields, Sections, or table columns
- any time you touch `*Form.php` or `*Table.php` in the admin panel

<core-rules>

These are mandatory. Do not skip any.

1. **Understand before you describe.** Never write copy mechanically or from the field name alone. First read the model, relationships, validation rules, observers, enums/constants, and how the value is consumed on the storefront/customer side. Copy written without that understanding is filler and fails the purpose of this skill.

2. **Every piece of copy answers three things** for a non-technical panel administrator:
   - **What is this?** — what it represents in plain business terms.
   - **Why does it matter?** — the purpose and the live-site/customer effect of setting it.
   - **What should I do?** — the concrete action/decision expected, plus constraints (formats, limits, immutability, recommended values).

   The test: a shop owner who has never seen the screen reads the copy and immediately knows the reason for the field and what to enter — without asking a developer. If it fails the test, rewrite it. See [references/copy-standards.md](references/copy-standards.md).

3. **Apply all required description surfaces:**
   - **Form field `->helperText()`** — required on every input whose purpose, format, constraint, or consequence is not 100% obvious from its label alone (toggles, selects with non-trivial options, slugs/codes, SEO, image uploads, money/format, any immutable-after-create field). A plain self-explanatory `name`/`email` may skip it.
   - **Section `->description()`** — required on every `Section` grouping more than a couple of fields; sets context for the whole group.
   - **Table `->description()` + `->emptyStateHeading()` + `->emptyStateDescription()`** — required on every resource table; an empty table is never bare.

4. **Never hardcode copy — route it through the project's translation layer.** Every label/helperText/description resolves through a translation call (e.g. `__('admin.{key}')`) rather than an inline literal. In a **multi-language project**, add each new key to every configured locale in the same change, with the fallback-locale value written first. In a **single-language project**, still centralize the copy in the translation layer, but there is no multi-locale parity requirement. Key-naming conventions and lang-file mechanics live in [references/translation-keys.md](references/translation-keys.md).

5. **Verify Filament syntax & imports before writing.** Form and table layers use different namespaces and methods; confirm via laravel-boost `search-docs` and mirror a sibling file's imports. Details in [references/filament-syntax.md](references/filament-syntax.md).

</core-rules>

<supporting-info>

## References

- [references/copy-standards.md](references/copy-standards.md) — how to write meaningful copy: the what/why/do model, worked before→after examples, parameterized hints, `helperText()` vs `hintIcon()`.
- [references/translation-keys.md](references/translation-keys.md) — key naming conventions, `lang/{locale}/admin.php` shape, 6-locale parity mechanics.
- [references/filament-syntax.md](references/filament-syntax.md) — form vs table namespaces/imports, which method belongs on which layer, `search-docs` verification, the standard code shape.

## Final check

Before finishing, confirm:

- you actually read/understood the feature before writing the copy
- every non-obvious field has a `helperText`; every multi-field `Section` has a `description`
- the table has a `description` + empty-state heading/description
- each copy answers what it is, why it matters, and what to do — never just restates the label
- a non-technical admin could act on the copy without asking a developer
- copy is centralized in the translation layer, not hardcoded; in a multi-language project every new key exists in all configured locales, fallback value written first

If any item fails, fix it before finishing. If you find an existing form/table nearby that lacks descriptions, surface the gap to the user rather than silently leaving it bare.

</supporting-info>
