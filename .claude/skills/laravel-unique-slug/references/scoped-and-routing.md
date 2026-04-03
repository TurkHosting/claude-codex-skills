# Scoped Slugs And Routing

## Scoped Slugs

If uniqueness is scoped by tenant, site, locale, parent, team, or similar:

- include the scope columns in the slug lookup query
- include the same scope columns in the database unique index
- document the chosen scope clearly

Do not mix global uniqueness in code with scoped uniqueness in the database.

## Routing

If public URLs use slugs:

- implement `getRouteKeyName(): string`
- return the slug column name
- align route model binding with the chosen slug strategy

Do not change route binding blindly if the project already uses another routing convention.
