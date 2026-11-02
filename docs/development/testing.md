# Testing

VerdinCore uses PHPUnit 10. The suite includes unit tests plus focused database and session tests.

## Run the suite

```bash
composer test
```

Equivalent direct command:

```bash
php vendor/bin/phpunit
```

Run only unit tests:

```bash
php vendor/bin/phpunit tests/unit
```

Run one file while developing:

```bash
php vendor/bin/phpunit tests/unit/ContentServiceTest.php
```

## Current coverage areas

The version `4.0.1` suite contains focused tests for:

- analytics and summary aggregation;
- groups, permissions, registration, and editor ownership;
- post and page editing, author selection, review flags, and featured toggles;
- blocks, block groups, topics, and topic groups;
- slug generation and Greek transliteration;
- scheduled publishing and automatic featuring;
- page caching and cache clearing;
- media storage and orphan cleanup;
- filesystem behavior and error-log handling;
- sitemaps, robots files, and structured data;
- cron orchestration and partial failures;
- system information and application health.

## Environment

Use the test configuration in `phpunit.dist.xml`. Tests must not connect to or modify production data. Database tests should use a dedicated disposable database and the framework's test support.

Some unit tests use virtual filesystems or injected paths. Preserve those seams when changing filesystem code so tests remain isolated from developer machines.

## What to add with a change

| Change | Minimum useful checks |
| --- | --- |
| Model query | Eligible, ineligible, empty, and relationship cases |
| Editor save | Valid save, validation failure, transaction rollback, authorization boundary |
| New feature flag | Enabled and disabled behavior |
| Cron task | Success, no-op, failure, repeated run, cache effect |
| Media behavior | MIME, filename, paths, partial cleanup, orphan detection |
| Public metadata | Canonical, robots, social image, structured-data shape |
| Member content | Guest denial, member access, cache headers, indexing controls |

## Pre-deployment checks

```bash
composer check-platform-reqs --no-dev
php spark routes
php vendor/bin/phpunit
```

Also run the production asset build and the smoke tests from the [deployment guide](../operations/deployment.md). Passing unit tests does not verify web-server routing, TLS, SMTP delivery, filesystem ownership, scheduler setup, or generated asset presence.

## Test quality

Prefer behavior-level assertions over private implementation details. A test should make the rule clear enough that a future reader understands why a failure matters. When fixing a defect, add a test that fails for the original behavior and passes for the corrected rule.
