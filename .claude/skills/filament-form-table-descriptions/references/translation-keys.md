# Translation Keys

All form/table copy is translated through `__('admin.{key}')` — never hardcoded. Keys live in `lang/{locale}/admin.php` and must exist in all 6 locales.

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

## 6-locale parity (non-negotiable)

- The six locales are `tr`, `en`, `ar`, `fr`, `es`, `ru`. Fallback is `en`.
- Whenever you add, rename, or edit a key, apply the same change to **all six** `lang/{locale}/admin.php` files in the same task.
- **Write the English value first**, then translate into the other five. Fallback is a safety net, not a translation strategy.
- Proper nouns (brand/product/theme names, third-party services) stay identical across all six — only translate the descriptive words around them.
- If you notice a pre-existing key that is missing in some locales, surface the gap to the user.

## Quick parity self-check

After adding keys, confirm each new key is present in every locale file before finishing. A key present in only some locales is a bug, not "covered by fallback."
