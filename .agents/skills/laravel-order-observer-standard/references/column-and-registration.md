# Column And Registration Rules

## Ordering Column

Use only one ordering column across the implementation:

- `position`
- `order`

If the project already uses one, continue with it. If the project is new, prefer `position`.

Keep the choice consistent across:

- database columns
- model attributes
- observers
- Filament forms
- Filament tables

## Observer Registration

Register observers with the PHP attribute:

```php
use Illuminate\Database\Eloquent\Attributes\ObservedBy;

#[ObservedBy(ModelObserver::class)]
class Model extends Eloquent
{
}
```

Do not move this pattern to service providers unless the project already does.
