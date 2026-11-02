# Database reference

VerdinCore `4.0.1` uses MariaDB or a compatible MySQL server with InnoDB and `utf8mb4`. A complete installation schema is stored in `database/schema.sql`.

This is a conceptual reference. The SQL schema remains authoritative for column types, indexes, foreign keys, and defaults.

## Editorial tables

| Table | Purpose | Important relationships |
| --- | --- | --- |
| `posts` | Dated content and publishing state | Optional author and featured media; many-to-many topics |
| `pages` | Hierarchical durable content | Optional author, featured media, and parent page |
| `topic_groups` | Top-level taxonomy groups | Has many topics |
| `topics` | Post classifications | Belongs to one topic group |
| `post_topics` | Post-to-topic join | Cascades when a post or topic is deleted |
| `block_groups` | Collections of reusable blocks | Has many blocks |
| `blocks` | Reusable content units | Belongs to one block group |
| `block_elements` | Typed block fields | Belongs to one block; optional media reference |
| `media` | Uploaded-file metadata | Optional uploader; referenced by content and image elements |

Posts and pages use soft deletion through `deleted_at`. Their status remains one of `draft`, `published`, `scheduled`, or `archived`.

## Authentication tables

CodeIgniter Shield owns the authentication data model:

| Table | Purpose |
| --- | --- |
| `users` | User account state and profile identity |
| `auth_identities` | Password, email, and action identities |
| `auth_groups_users` | Group membership |
| `auth_permissions_users` | Direct user permissions |
| `auth_logins` | Login attempts |
| `auth_remember_tokens` | Remember-me tokens |
| `auth_token_logins` | Token authentication log |

Foreign keys from Shield identity, group, permission, and remember-token tables cascade when a user is deleted. Content author and media-uploader references become null instead of deleting editorial data.

## Analytics and operations

| Table | Purpose |
| --- | --- |
| `stats_visitor` | One hashed visitor record per date with a view count |
| `stats_summary` | Aggregated daily visitors, views, and published-post counts |
| `cron_runs` | Trigger, status, task-result JSON, and timestamps for each maintenance run |
| `github` | Synchronized metadata for public repositories |
| `settings` | Framework settings storage |
| `migrations` | Framework migration state |

## Key integrity rules

- Post, page, topic, and topic-group slugs are unique within their table.
- A post can be assigned to a topic only once.
- A block handle is globally unique.
- An element handle is unique within its block.
- A media filename is unique within its collection.
- Visitor fingerprints are unique per date.
- Daily summaries are unique per date.
- GitHub repository IDs are unique.

Foreign keys protect the main relationships. Some delete behavior is deliberately handled by services and cleanup jobs because files and editorial references need coordinated work.

## Content columns

Posts and pages store:

- optional author and featured-media IDs;
- title, slug, body, and excerpt;
- status and audience level;
- presentation and review flags;
- word count and decimal reading time;
- views and last-viewed time;
- publication, creation, update, and soft-delete times.

Posts add unlisted and video flags. Pages add parent, sort order, about, and contact fields.

## Time handling

Scheduled publication values are normalized to UTC before storage. The scheduler compares the stored time with current UTC. Display formatting may use the application or user-facing timezone, but persistence and scheduling logic should stay consistent.

## Schema lifecycle

New installations import the complete SQL schema. The application migration directory contains no product migrations in version `4.0.1`.

For upgrades:

1. back up the existing database;
2. compare the release schema and release notes;
3. prepare explicit, reviewed changes;
4. test them against a restored copy;
5. deploy application and schema changes as a compatible pair.

Never import the complete schema over a populated production database.
