# Model Pattern

## Rules

- Implement `HasMedia` and use `InteractsWithMedia`.
- Register each media collection explicitly.
- Use `->singleFile()` by default unless the collection is intentionally multi-file.
- Keep image dimensions in model constants only.

## Preferred Constant Names

- `IMAGE_WIDTH`
- `IMAGE_HEIGHT`
- `HEADER_IMAGE_WIDTH`
- `HEADER_IMAGE_HEIGHT`
- `OG_IMAGE_WIDTH`
- `OG_IMAGE_HEIGHT`

## Anti-Patterns

- Do not store image paths or filenames in normal columns for media-library-managed images.
- Do not duplicate image dimensions across forms, seeders, and conversions.
