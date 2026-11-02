# Feature map

This page summarizes functionality present in VerdinCore `4.0.1`. Some features are disabled by default and must be enabled per deployment.

## Publishing

- Posts and hierarchical pages.
- Draft, scheduled, published, and archived states.
- A separate pending-review flag.
- Soft deletion, restore, and permanent deletion.
- UTC publication scheduling for posts and pages.
- Public and member-only access levels.
- Featured content, unlisted posts, video flags, about pages, and contact pages.
- Word count, estimated reading time, views, and last-viewed time.
- Unique slugs, including Greek to Latin transliteration for generated slugs.
- Optional author reassignment for authorized editors.

## Navigation and discovery

- Home and optional blog indexes.
- Date archive with optional text search.
- Topic and topic-group archives.
- Related, featured, popular, and recently viewed post components.
- Hierarchical page URLs and breadcrumbs.
- Configurable pagination and sidebar limits.
- Optional RSS 2.0 feed with excerpts or full content.

## Editing

- Tiptap-based full and compact rich-text editors.
- Links, managed images, embeds, YouTube content, and code-aware public rendering.
- Server-side HTML sanitization before storage.
- Transactional saves when content and relationships change together.
- Validation for status, access, hierarchy, slugs, schedules, topics, blocks, and media references.

## Reusable blocks

- Named block groups.
- Globally unique block handles.
- Element handles scoped to a block.
- Text, rich-text, link, and image element types.
- Template access to one value or a complete group.
- Orphan cleanup for replaced or deleted block media.

## Media

- Featured post, featured page, inline content, and block collections.
- MIME validation, dimension checks, and randomized storage names.
- EXIF-aware image processing.
- AVIF originals and responsive variants for featured and inline images.
- JPEG social-sharing variants for featured images.
- Original-file storage for block images, including SVG where configured.
- Media library filtering by collection, uploader, and sort order.

## Identity and presentation

- Four bundled brand configurations: `verdincore`, `verdindynamics`, `tomgineer`, and `portfolio`.
- Independent brand and theme selection.
- Per-brand identity, contact data, locale, domain, social links, GitHub owner, and Schema.org identity.
- Brand logos, icons, profile images, sharing images, and favicons.
- Separate production bundles for the public theme and admin UI.

## Search visibility

- Canonical URLs and standard metadata.
- Open Graph and Twitter card output.
- Schema.org graphs for sites, identities, posts, pages, collections, and breadcrumbs.
- Special `AboutPage`, `ProfilePage`, and `ContactPage` types when a page is flagged accordingly.
- XML and gzip-compressed sitemaps.
- Managed `robots.txt` directives.
- `noindex`, private caching headers, and omission from structured data for member-only content.

## Authentication and administration

- CodeIgniter Shield session authentication.
- Optional public registration.
- Email-code account activation with a resend cooldown.
- `superadmin`, `admin`, `editor`, and `member` groups.
- Permission checks on every administrative section and mutation route.
- Dashboard views for content, taxonomy, blocks, media, users, trash, analytics, cron history, logs, and system information.
- Manual cache clear, cron execution, and log cleanup for users with system permission.

## Analytics and automation

- First-party daily visitor and page-view counts for the tracked home and post routes.
- Separate published-post view counts.
- Exclusion of authenticated users and detected crawlers.
- Production page caching for guest GET and HEAD requests.
- Scheduled publication and optional automatic featured-post selection.
- Conditional cache clearing after content-changing jobs.
- Media and expired-session cleanup.
- Optional synchronization of public GitHub repositories.
- Per-run cron results with `success`, `partial`, or `failed` status.
