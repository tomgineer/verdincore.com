# Editorial workflow

This guide explains how posts and pages move through VerdinCore. Exact dashboard access depends on the user's permissions.

## Posts and pages

Use a post for dated editorial content that belongs in indexes, feeds, archives, and topic collections. Use a page for durable content that may belong in a hierarchy, such as an about page, services section, or contact page.

Both editors support:

- title, excerpt, and rich HTML content;
- a unique slug;
- public or member-only access;
- draft, scheduled, published, and archived states;
- a featured image;
- featured and pending-review flags;
- word count and reading time;
- optional author reassignment when enabled.

Posts also support topics, unlisted delivery, video labeling, and republishing. Pages support parent selection, sibling order, and about/contact flags.

## Create a post

1. Open the post editor and choose **New Post**.
2. Write the title, excerpt, and body.
3. Add topics when taxonomy is in use.
4. Choose the audience and any display flags.
5. Upload or select a featured image if needed.
6. Save as a draft, publish immediately, or choose scheduled status and a UTC date and time.

Post slugs are generated from the title. The slug service normalizes the value, supports Greek to Latin conversion, enforces the maximum length, and adds a suffix when needed for uniqueness.

Saving a post and synchronizing its topics happen in one database transaction. A failure in either part rolls the whole save back.

## Create a page

The page workflow is similar, with three extra decisions:

- **Parent:** places the page below another page and becomes part of its URL.
- **Sort order:** controls ordering among pages in the same part of the hierarchy.
- **Page purpose:** about and contact flags influence Schema.org output.

Page slugs are editable. They must be unique across pages and contain only supported slug characters.

The application prevents a page from becoming its own parent and rejects any parent choice that would create a cycle.

## Statuses and publication time

| Status | Public behavior |
| --- | --- |
| `draft` | Not publicly available |
| `scheduled` | Not available until the configured UTC time is reached and cron runs |
| `published` | Available when the audience and path rules permit it |
| `archived` | Removed from public delivery without being deleted |

When content first moves to `published`, the application records the current UTC time. A scheduled item stores its future time in the same publication field. Moving a scheduled item back to draft or archived clears that time.

The post editor also has a republish action. It is available only for a published post and updates the publication time to now, which moves the post in date-ordered lists.

## Review flag

`is_pending_review` is independent from status. It can be used to mark work that needs attention without introducing another publication state. The current application does not implement a multi-step approval engine, so editorial policy should define who may clear the flag and publish.

## Public and member-only content

The access level is either `public` or `members`.

- Guests can read public content only.
- Logged-in users can also read member content.
- Member content is sent with private, no-store response headers and `noindex` directives.
- Member content does not produce Schema.org JSON-LD.
- A member-only page makes its full descendant path private for guests.
- Member content cannot be marked featured, about, or contact by the save service.

Use member access for authenticated delivery, not as a substitute for fine-grained authorization. All logged-in groups can pass the current member-content visibility check.

## Featured and unlisted posts

A featured flag makes a public post eligible for featured presentation. If automatic featuring is enabled, the daily job recalculates the featured set using the configured ratio and threshold.

An unlisted post remains available at its direct URL but is left out of public lists, navigation counts, sitemaps, and automatic featuring. This is useful for link-only content, but it is not private. Use member access for content that should require authentication.

## Trash and restoration

Moving a post or page to trash uses soft deletion. The trash dashboard provides two distinct actions:

- **Restore:** removes the deletion marker without changing the item's previous status or content fields.
- **Delete permanently:** removes the record and cannot be undone through the application.

Because restore preserves status, a restored item that was previously published can become publicly eligible again immediately. Review its status and audience as part of the restore action. Review linked media and backups before permanent deletion.

## Content safety

The editor sends HTML, but the server sanitizes it before storage. The full-content allowlist supports safe HTML plus the configured iframe, YouTube, responsive-image, and media attributes. Empty sanitized content is stored as a minimal paragraph.

The maximum input size for a post or page body is 5,000,000 bytes. Model validation separately enforces title length, slug shape, status, audience, dates, and numeric fields.
