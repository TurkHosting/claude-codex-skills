# Filament Integration

## Upload Fields

Use `SpatieMediaLibraryFileUpload`.

Each field should include:

- `->collection('...')`
- `->conversion('web')`
- `->label('...')`
- `->helperText(...)` from model constants
- `->rule(Rule::dimensions()...)` from model constants
- `->image()`
- `->acceptedFileTypes(['image/jpeg', 'image/png'])`
- `->maxSize(...)`
- `->visibility('public')`
- `->openable()`

Use `columnSpanFull()` when the upload should span the full layout width.

For singleton pages, bind the upload explicitly to the singleton model instance.

## Table Columns

Use `SpatieMediaLibraryImageColumn`.

Usually include:

- `->collection('...')`
- `->conversion('web')`
- `->label('...')`

Optionally include:

- `->imageSize(...)`
- `->circular()`
