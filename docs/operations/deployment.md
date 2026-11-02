# Deployment

This guide describes a conventional production release. Adapt process ownership, service restarts, and rollback steps to the hosting environment.

## Before the release

1. Confirm the intended VerdinCore version from `VERSION`.
2. Review application, schema, dependency, and environment changes.
3. Back up the database and `public/images/` as one recovery point.
4. Run the complete test suite.
5. Build production assets and verify the selected theme.
6. Prepare a rollback package and database plan.

Do not treat `database/schema.sql` as an in-place upgrade script.

## Install production dependencies

```bash
composer install --no-dev --optimize-autoloader
npm ci
npm run build
```

Assets can be built in a trusted build environment and shipped with the release. If Node.js is not available on production, make sure every generated file below `public/assets/themes/` is included in the artifact.

## Production environment

At minimum:

```dotenv
CI_ENVIRONMENT = production
app.baseURL = 'https://example.com/'
app.forceGlobalSecureRequests = true
cookie.secure = true
verdincore.cache.enabled = true
```

Supply database and email credentials through the deployment's protected secret mechanism. Keep debug output disabled.

## Web server

Point the document root to `public/`. Never expose the repository root, `app/`, `writable/`, `brands/`, `.env`, Composer files, or build sources as web content.

Send requests that do not match a real public file to `public/index.php` according to the CodeIgniter deployment guidance for the chosen server.

## Writable data

The runtime account needs narrowly scoped write access to:

- `writable/`;
- `public/images/`;
- `public/robots.txt`;
- `public/sitemap.xml`;
- `public/sitemap.xml.gz`.

Compiled assets and application source should remain read-only for the web process where practical.

## Scheduler

Install one daily invocation from the application root:

```cron
0 2 * * * cd /var/www/verdincore && /usr/bin/php public/index.php cron >/dev/null 2>&1
```

Use an absolute PHP path and a working directory that contains the correct `.env`. Capture output through the platform's scheduler or monitoring system if sending it to `/dev/null` would hide failures.

## Release sequence

1. Put the site into the deployment's chosen maintenance mode if the change requires it.
2. Upload or switch to the complete release artifact.
3. Apply reviewed database changes, if any.
4. Verify environment settings and writable ownership.
5. Clear stale application and PHP opcode caches.
6. Run the route and test checks appropriate for production.
7. Run cron once and inspect the recorded result.
8. Remove maintenance mode.
9. Perform the smoke tests below.

## Smoke tests

- Home, blog, archive, post, and hierarchical page routes behave as configured.
- Guest and member visibility differ correctly.
- Login, logout, activation, and admin authorization work.
- A draft can be saved without publication.
- A scheduled item publishes when due.
- Media upload returns all expected variants.
- CSS, JavaScript, fonts, brand images, and favicons return 200 responses.
- Feed, sitemap, compressed sitemap, and robots output are correct when enabled.
- A guest response can be cached, while an authenticated response is not.
- Cron history, analytics, error logs, and system information load for an authorized administrator.

## Rollback

Application rollback and data rollback are separate decisions. Restoring old code against a newer schema can be as unsafe as restoring the database alone.

If rollback is required:

1. stop writes or enable maintenance mode;
2. restore a mutually compatible application release and database backup;
3. restore the matching `public/images/` snapshot when media or references changed;
4. clear cache and opcode state;
5. run the smoke tests before reopening the site.
