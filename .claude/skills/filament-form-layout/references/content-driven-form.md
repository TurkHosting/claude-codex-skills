# Content-Driven Form

Apply this layout to blog, page, article, documentation, and other forms where content is the primary focus.

## Rules

- Use a 3-column layout with a sidebar.
- Keep title, slug, body, and images in the main content area.
- Keep status, `published_at`, author, categories, tags, and similar controls in the sidebar.
- Treat SEO as secondary content. Place it after the main content or in a collapsed section.
- Keep the main content area visually dominant.

## Example

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
