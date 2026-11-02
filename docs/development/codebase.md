# Codebase guide

This guide is for developers with authorized access to the private application source.

## Project layout

```text
app/
  Authentication/  Custom Shield activation behavior
  Cells/           Reusable server-rendered components
  Config/          Routes, services, auth, images, framework configuration
  Controllers/     Public, admin, and system entry points
  Exceptions/      Domain-specific validation failures
  Filters/         Full-page cache and tracking integration
  Helpers/         View-facing helper functions
  Libraries/       Media, filesystem, input, logs, and utility classes
  Models/          Queries, validation, relationships, and persistence
  Objects/         Read-only block value objects
  Services/        Application, admin, SEO, analytics, and cron workflows
  Views/           Public-theme namespaces and administration views
brands/            Brand configuration and source identity assets
build-scripts/     Tailwind and esbuild orchestration
database/          Complete installation schema
public/            Web document root, compiled assets, runtime public files
tests/             PHPUnit database, session, unit, and support code
themes/            Public and administration front-end sources
writable/          Cache, logs, sessions, uploads, and framework runtime data
```

## Entry points

- `app/Config/Routes.php` declares public, auth, admin, and CLI routes.
- `app/Controllers/Site.php` serves the public site.
- `app/Controllers/Admin/` handles protected editor and dashboard requests.
- `app/Controllers/System/` contains cron, feed, activation-success, and error entry points.
- `app/Config/Services.php` wires application services and their dependencies.

Start a behavior change at the route and controller, then follow the service and model calls. UI-only changes usually begin in `app/Views/` or `themes/`.

## Service boundaries

Central services include:

| Service | Responsibility |
| --- | --- |
| `ContentService` | Page trees, flagged pages, navigation taxonomies, archives, breadcrumbs |
| `BlockService` | Resolve individual block values and complete groups |
| `FeedService` | Generate RSS 2.0 XML |
| `MetaService` | Canonical, social, author, image, and robots metadata |
| `StructuredDataService` | Schema.org graphs for public views |
| `TrackingService` | First-party visitor and post-view tracking |
| `CronService` | Coordinate independent maintenance tasks and record results |
| `Admin\EditService` | Transactional post and page editing |
| `Admin\DashboardService` | System actions and trash operations |
| `Admin\*EditService` | Validate and persist users, taxonomy, blocks, and groups |

Use CodeIgniter services for shared application dependencies. Preserve explicit constructor dependencies so unit tests can supply controlled models and helpers.

## Models and queries

Models contain persistence validation and reusable query scopes. Public visibility should go through the existing `visibleTo()` and published-content scopes rather than being reproduced in controllers.

Keep these concerns aligned when adding a field:

1. SQL schema;
2. model `allowedFields` and validation;
3. editor input and service save data;
4. list and public query selection;
5. views and metadata;
6. tests;
7. public documentation when behavior changes.

## Error behavior

Missing public records and invalid page paths become 404 responses. Expected editorial validation uses domain-specific exceptions and user-facing errors. Unexpected storage and transaction failures use runtime exceptions and logs.

Tracking is deliberately fail-open for the completed public response. Cron tasks isolate failures so later tasks can run. Do not generalize those choices to security checks, authorization, or content persistence.

## Adding a feature flag

1. Add a typed default to `Config\Verdincore`.
2. Add the documented environment key to `env` when operators should control it.
3. Read the value at the narrowest appropriate behavior boundary.
4. Add tests for enabled and disabled states.
5. Document its default, effect, and operational interactions.

## Adding a brand

Follow the [brands and themes guide](../guides/brands-and-themes.md). Brand configuration is allowlisted, so adding a directory alone is not sufficient.

## Adding a cron task

1. Create a focused class below `app/Services/Cron/` with a `run()` method.
2. Register its dependency in `Config\Services`.
3. Insert it in the required `CronService` order.
4. Decide whether its changes require cache invalidation.
5. Make failures safe to retry where possible.
6. Add unit tests for success, no-op, failure, and repeated execution.
7. Update [scheduled jobs](../operations/scheduled-jobs.md).

## Coding considerations

- Keep public and member visibility explicit.
- Use transactions for multi-record editorial writes.
- Treat handles and slugs as external contracts.
- Resolve filesystem paths through the media and filesystem libraries.
- Keep private configuration and credentials out of source and test output.
- Update the complete schema deliberately because product migrations are not currently used.
