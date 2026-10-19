# Security and operations

This document describes platform-level controls visible in the current architecture. It is not a deployment security guarantee or a substitute for environment-specific review.

## Access control

- Authentication is provided by CodeIgniter Shield.
- Administrative routes require both a valid session and membership in the admin group.
- Public, admin, and CLI routes are declared explicitly.
- Automatic route discovery is not relied on for protected actions.

## Request and response controls

- HTTPS is enforced through the global filter pipeline.
- CSRF protection is enabled for state-changing browser requests.
- Secure response headers are applied globally.
- Administrative mutations use POST routes.
- Framework validation and application-specific validation run before persistence.

Deployment configuration still matters. Cookie settings, trusted hosts, proxy behavior, database permissions, PHP configuration, and web server rules must be appropriate for the target environment.

## Content safety

Rich content is sanitized on the server before storage. Full editor content and compact block content use separate allowlists suited to their roles.

The editing services also validate:

- content length and required fields;
- slugs and uniqueness;
- page hierarchy integrity;
- topic and group relationships;
- supported block element types;
- link values and image references;
- publication state and schedule values.

Multi-record saves use database transactions so partial updates do not leave content relationships in an inconsistent state.

## Media handling

The upload pipeline does not trust the original filename or extension. It validates the detected media type, assigns a cryptographically random name, creates configured variants, and cleans incomplete files if processing fails.

Separate upload endpoints and collections preserve the intended relationship between media and posts, pages, inline content, or blocks.

## Page caching

Full-page caching is optional and applies only when all of the following are true:

- the application is running in production;
- caching is enabled for the deployment;
- the request belongs to a guest session;
- the controller has explicitly marked the response as cacheable.

Authenticated users do not receive or create public page-cache entries. When the scheduled publisher changes content, it clears the application cache before completing the job.

## First-party tracking

VerdinCore includes a deliberately small analytics path:

1. Only successful public GET requests in the supported tracking scope are considered.
2. Authenticated users and detected crawlers are excluded.
3. A random visitor token is stored in an HTTP-only, same-site cookie.
4. Only a SHA-256 hash of that token is stored by the application.
5. Daily totals are summarized by a recurring job.

The original token is not stored in the database. Deployments remain responsible for providing appropriate privacy information and consent behavior for their jurisdiction and use case.

## Scheduled operations

The coordinated maintenance process can run from the CLI or a protected admin action. It records who triggered the run, timestamps, completed jobs, and a success, partial, or failed state.

Current recurring tasks cover:

- analytics aggregation;
- orphaned media cleanup;
- expired session cleanup;
- scheduled content publication;
- XML and gzip sitemap generation;
- robots directive verification;
- optional GitHub repository synchronization.

Failures are recorded and rethrown so the surrounding scheduler or administrator can detect them.

## Observability

The admin dashboard exposes cron history, error logs, analytics, and system information. Application-level tracking failures are isolated from completed public responses and written to logs for later investigation.

Automated tests cover core services including publishing, blocks, taxonomies, analytics, file behavior, structured data, media cleanup, slug generation, and SEO files.

## Public reporting

Do not open a public issue containing credentials, personal information, exploit details, private URLs, environment values, or infrastructure data. A private security reporting channel will be documented when one is formally available.

