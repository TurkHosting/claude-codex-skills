# Translation Keys

All form/table copy is translated through a translation call such as `__('admin.{key}')` — never hardcoded. Keys live in a translation namespace such as `lang/{locale}/admin.php`. In a multi-language project the key must exist in every configured locale; in a single-language project the same key simply lives in that one locale.

## Key naming conventions

Follow these exactly — the panel already uses them:

```
admin.{field}                        → field / column label        (admin.code, admin.email)
admin.{field}_hint                   → field helperText            (admin.currency_code_hint)
admin.{field}_placeholder            → field placeholder           (admin.facet_code_placeholder)
admin.{section}_description          → Section description         (admin.currency_identity_description)
admin.{resource}_page_description    → table description           (admin.currencies_page_description)
admin.{resource}_empty_heading       → table empty-state heading   (admin.currencies_empty_heading)
admin.{resource}_empty_description   → table empty-state body      (admin.currencies_empty_description)
```

For fields reused panel-wide, prefer a shared key over a per-resource duplicate:

```
admin.is_active_hint
admin.meta_title_hint
admin.meta_description_hint
```

## Translation file shape

`lang/{locale}/admin.php` is a flat associative array, grouped by feature area with comments:

```php
// Shared form hints (panel-wide helperText standard)
'is_active_hint'      => 'When turned off, the item is hidden from the storefront without being deleted.',
'meta_title_hint'     => 'Shown as the clickable title in search engine results; falls back to the normal name if left empty.',
'customer_email_hint' => 'The customer signs in with this address and receives order emails here.',
```

## Locale parity (multi-language projects only)

Applies only when the project ships more than one locale. Single-language projects keep the key in their one locale and can skip the parity rules below.

- Determine the project's **configured locales and its fallback from its own config** — do not assume a fixed set.
- Whenever you add, rename, or edit a key, apply the same change to **every** configured locale file in the same task.
- **Write the fallback-locale value first**, then translate into the rest. Fallback is a safety net, not a translation strategy.
- Proper nouns (brand/product/theme names, third-party services) stay identical across locales — only translate the descriptive words around them.
- If you notice a pre-existing key that is missing in some locales, surface the gap to the user.

## Quick parity self-check

In a multi-language project, after adding keys confirm each new key is present in every configured locale file before finishing. A key present in only some locales is a bug, not "covered by fallback."
