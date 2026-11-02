# Administration and roles

VerdinCore uses CodeIgniter Shield groups and permissions. Dashboard access is permission-based, not a single administrator boolean.

## Groups

| Group | Intended access |
| --- | --- |
| `superadmin` | Complete content, system, media, and user management |
| `admin` | Complete content, media, and system access without user management |
| `editor` | Create posts, edit own posts, and upload media while editing authorized content |
| `member` | Read public and member-only content without dashboard access |

New registrations join `member` by default.

## Permission matrix

| Permission | Protects |
| --- | --- |
| `dashboard.access` | Entry to an authorized dashboard section |
| `admin.access` | Full overview area |
| `posts.create` | New posts |
| `posts.edit-own` | Posts authored by the current user |
| `posts.edit-any` | Posts by any author |
| `posts.delete` | Post trash actions |
| `pages.manage` | Page creation, editing, and deletion |
| `topics.manage` | Topics and topic groups |
| `blocks.manage` | Blocks and block groups |
| `media.upload` | Editor upload endpoints |
| `media.manage` | Media library |
| `system.manage` | Cron, cache, analytics, logs, and system information |
| `users.manage` | User creation, editing, blocking, role assignment, and deletion |

Route filters enforce these permissions before a controller action runs. Services also apply ownership and validation rules where a route permission alone is not enough.

## Dashboard sections

Depending on permission, the dashboard exposes:

- overview and at-a-glance data;
- post and page lists with filters and pagination;
- topic and topic-group management;
- block and block-group management;
- media library;
- trash and restoration;
- users;
- analytics;
- cron run history;
- application error logs;
- system and environment information.

## System actions

Users with `system.manage` can:

- clear the configured cache;
- run the full maintenance workflow immediately;
- clear application error log files.

A manual maintenance run is recorded with `triggered_by = admin`. Partial failures are reported as informational completion, with per-task results available in cron history.

## Registration and activation

Public registration is disabled by default. When enabled, the registration route is added and a new user must complete email-code activation.

The custom activation flow reuses the existing code when resending and applies a 60-second session cooldown. SMTP must be configured before registration is exposed.

## First administrator

The application does not grant administrative access to the first registration automatically. An authorized operator must assign the first trusted user to `superadmin` directly in the database or through an existing privileged account.

For a new database, verify the user ID before applying the group change:

```sql
SELECT id, username FROM users;

UPDATE auth_groups_users
SET `group` = 'superadmin'
WHERE user_id = 1;
```

Replace `1` with the verified ID. Do not promote an account until its identity and ownership have been confirmed.

## User-management safeguards

User editing is centralized in the user service, which validates group changes and actor identity. Treat `superadmin` assignment and deletion as privileged operations. Keep at least one verified recovery path for administration and include authentication tables in database backups.
