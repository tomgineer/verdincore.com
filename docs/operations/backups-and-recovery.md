# Backups and recovery

VerdinCore content spans the database and filesystem. A usable recovery point must keep them together.

## What to back up

| Data | Why it matters |
| --- | --- |
| MariaDB database | Content, users, authorization, media metadata, analytics, settings, and job history |
| `public/images/` | Uploaded files and generated variants referenced by media records |
| Protected environment configuration | Base URL, database, email, feature, cache, cookie, brand, and theme settings |
| Brand and theme sources | Site identity and presentation when not already recoverable from the release artifact |
| Release artifact or Git reference | Exact application version compatible with the data |

Generated bundles can be rebuilt from source, but retaining the deployed artifact makes recovery faster and removes uncertainty about tool versions.

Logs, cache, sessions, sitemaps, and robots files have different recovery value. Preserve logs according to the incident and retention policy. Cache and sessions can usually be regenerated or intentionally discarded.

## Consistency

Media metadata and image files must describe the same point in time. Use a backup method that prevents writes briefly, creates coordinated snapshots, or records enough database and filesystem timing information to reconcile them.

Running orphan cleanup against a restored database and a mismatched image directory can delete files that belong to another backup point.

## Suggested policy

- Take automated database and media backups at a frequency that matches acceptable data loss.
- Keep more than one generation and at least one copy outside the application host.
- Encrypt backup data at rest and in transit.
- Restrict restore access more tightly than ordinary dashboard access.
- Record the VerdinCore version and schema state with every backup.
- Test a full restore on an isolated environment regularly.

## Restore procedure

1. Isolate the target and prevent public writes.
2. Deploy the application version recorded with the backup.
3. Restore the database into an empty, verified target.
4. Restore the matching `public/images/` snapshot.
5. Restore protected environment settings through the secret-management process.
6. Rebuild or restore public asset bundles.
7. Recreate narrow runtime permissions.
8. Clear application cache and stale sessions.
9. Run tests and a manual cron cycle in the isolated environment.
10. Perform the deployment smoke tests before directing traffic to the restored site.

## Recovery checks

- Counts of posts, pages, users, topics, blocks, and media are plausible.
- Featured and inline images return successfully.
- Page hierarchies and member-only visibility are intact.
- Login and role assignments work.
- A media upload can write every required variant.
- Sitemap and robots files regenerate.
- Cron records a successful or understood partial result.
- Cache does not contain pages from the previous environment.

## Permanent deletion

The dashboard's permanent-delete action cannot be undone in the application. Recovery requires an earlier database backup and, when media is involved, the corresponding image backup. Document retention limits so operators know when an item has passed the recovery window.
