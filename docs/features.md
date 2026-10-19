# Features

This page describes capabilities implemented in the current private core. Individual deployments can disable or configure some features.

## Content publishing

- Create and edit posts and hierarchical pages.
- Move content through draft, scheduled, published, and archived states.
- Schedule publication using a normalized UTC workflow.
- Add excerpts, featured images, publication details, and display flags.
- Feature selected pages and posts in navigation or content sections.
- Move items to trash, restore them, or permanently remove them.
- Generate unique, readable slugs, including Greek to Latin transliteration support.
- Render nested page paths and breadcrumbs from the page hierarchy.

## Taxonomy and discovery

- Organize posts with topics.
- Collect topics into topic groups.
- Browse topic and topic-group archives.
- Surface related, featured, popular, and recently viewed posts.
- Provide chronological archive navigation.
- Publish a configurable RSS feed.

## Reusable blocks

- Define blocks by stable handles so templates can request content without hard-coded database identifiers.
- Group related blocks into ordered block groups.
- Compose blocks from text, rich text, link, and image elements.
- Validate and sanitize block content before storage.
- Reorder elements while preserving a structured content model.
- Remove media that is no longer referenced by a block.

## Editing experience

- Use Tiptap-based rich text editors for full content and compact structured fields.
- Insert managed images, links, embedded content, and YouTube media.
- Upload featured, inline content, page, and block images through dedicated flows.
- Keep complex save operations consistent with database transactions.
- Receive server-side validation for content, slugs, relationships, schedules, and media.

## Media pipeline

- Validate uploaded file types before accepting them.
- Generate randomized filenames rather than trusting client filenames.
- Produce configured image formats and resized variants.
- Store media metadata and its relationship to content.
- Clean incomplete uploads when processing fails.
- Remove orphaned featured, content, and block media through maintenance jobs.

## Multi-brand delivery

- Select a supported brand per deployment.
- Configure display name, slogan, description, author, and copyright text.
- Configure domain, locale, contact details, social links, and GitHub identity.
- Supply brand-specific logos, icons, favicons, profile images, and social sharing images.
- Select a public theme independently of the application core.
- Enable site-level features such as blog views, archives, topics, RSS, related posts, and page caching.

## SEO and structured presentation

- Generate canonical URLs and standard HTML metadata.
- Generate Open Graph and Twitter card metadata.
- Build Schema.org JSON-LD for the website, posts, pages, archives, breadcrumbs, and configured identities.
- Generate XML and gzip-compressed sitemaps from published content.
- Maintain required `robots.txt` directives.
- Use semantic server-rendered pages that remain meaningful without client-side application bootstrapping.

## Administration

- Protect the admin area with CodeIgniter Shield authentication and admin-group authorization.
- Manage posts, pages, topics, topic groups, blocks, block groups, media, and users.
- Filter and paginate content lists.
- Review trash, analytics, cron history, error logs, and system information.
- Run the maintenance workflow from the command line or the protected admin context.

## Analytics and integrations

- Count first-party daily visitors using a random cookie whose original value is not stored.
- Track published post views separately from visitor totals.
- Exclude known crawlers and authenticated users from public visitor statistics.
- Aggregate daily statistics for efficient reporting.
- Optionally synchronize public repositories for a configured GitHub account.

## Performance and maintenance

- Cache complete public pages in production when enabled.
- Bypass public page caching for authenticated sessions.
- Build minified theme and admin assets with Tailwind CSS and esbuild.
- Publish scheduled pages and posts automatically.
- Clean expired session files and unreferenced media.
- Record maintenance runs as successful, partial, or failed.
