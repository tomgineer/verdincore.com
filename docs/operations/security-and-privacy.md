# Security and privacy

This page documents controls present in VerdinCore `4.0.1`. It is not a deployment guarantee and does not replace environment-specific threat modeling, hardening, or legal review.

## Authentication and authorization

CodeIgniter Shield provides session authentication, identities, groups, permissions, remember tokens, and login records.

Administrative routes require a valid session and `dashboard.access`. Specific permissions then protect content types, media, users, and system operations. Mutations use POST routes. Automatic route discovery is not used as a substitute for declared protected routes.

Public registration is configurable and disabled by default. When enabled, email activation uses a six-digit code flow supplied by Shield, with a custom 60-second resend cooldown in the current application.

## Request controls

The global filter pipeline includes:

- forced HTTPS behavior driven by application configuration;
- CSRF protection;
- secure response headers;
- optional full-page caching;
- post-response tracking;
- performance metrics.

Deployment configuration still determines the real security posture. Review trusted hosts, proxy headers, TLS termination, cookie flags, database privileges, PHP settings, filesystem ownership, and web-server rules.

## Content sanitization

HTML is sanitized on the server before storage.

Full post and page content uses the sanitizer's safe elements plus configured support for iframes, YouTube wrappers, managed-image IDs, `srcset`, and `sizes`. Relative links and media URLs are allowed.

Compact block rich text has a smaller allowlist: paragraphs, line breaks, strong and emphasized text, links, and ordered or unordered lists. Link targets and `rel` attributes are forced to safer values.

Sanitization is backed by model and service validation for titles, slugs, statuses, access levels, dates, page hierarchies, block types, media IDs, and relationships.

## Media safety

Uploads are checked using detected MIME information and image processing, not the original extension alone. Public filenames are random. Deletion routines validate collection names and expected filename shapes before resolving stored paths.

SVG is accepted only for the original-file block collection. Since SVG can carry active content, deployments should review how user-supplied block SVG files are served and who receives `blocks.manage`.

## Member content

Member-only delivery includes several protections:

- guest queries exclude member records;
- hierarchical pages verify the audience of every ancestor;
- responses use `Cache-Control: private, no-store, max-age=0` and `Pragma: no-cache`;
- `X-Robots-Tag` and page metadata use `noindex, nofollow, noarchive`;
- structured data is omitted;
- authenticated requests bypass public page caching.

Member access is group-neutral in the current visibility model. Any logged-in user can read member content. It should not be used for content that needs per-user or per-role authorization.

## First-party analytics

Tracking runs only for successful public GET requests in its supported scope, which currently covers the home route and individual post routes. It excludes logged-in users and known crawlers.

For a new visitor, the application creates a random 32-byte token and stores it in the `vc_visitor` cookie for one year. The cookie is HTTP-only, uses `SameSite=Lax`, covers the whole site, and is secure when the request is HTTPS. Only a SHA-256 hash of the token is stored in MariaDB.

The application separately increments views for published public post URLs. Tracking failures are logged without interrupting the page response.

The original token is not stored by the application, but the cookie and daily activity still require an appropriate privacy notice and, where applicable, consent or another lawful basis.

## Secrets and sensitive output

- Never commit `.env`.
- Keep database, SMTP, API, and infrastructure credentials in a protected secret store.
- Do not expose `writable/logs`, system information, or cron details publicly.
- Limit dashboard system permissions to trusted operators.
- Redact secrets and personal data before sharing logs.
- Keep production error display disabled.

## Responsible reporting

Do not open a public issue containing credentials, personal data, exploit steps, private URLs, or infrastructure details. Until a private reporting channel is published, contact the copyright holder through an established private business channel.

## Operational hardening checklist

- The document root is exactly `public/`.
- HTTPS and secure cookies are enabled in production.
- Registration is disabled unless needed and monitored.
- SMTP credentials are protected and sender authentication is configured.
- Database credentials have only the required privileges.
- Source files are read-only to the web process where possible.
- Runtime write permissions are limited to documented paths.
- Backups are encrypted, access-controlled, and restore-tested.
- Dependencies, PHP, MariaDB, and the web server receive security updates.
- Cron, authentication failures, logs, and storage capacity are monitored.
