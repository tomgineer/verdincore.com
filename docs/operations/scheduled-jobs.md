# Scheduled jobs

VerdinCore exposes one CLI-only route that coordinates daily maintenance:

```bash
php public/index.php cron
```

It can also be run from the dashboard by a user with `system.manage`.

## Execution order

The current workflow runs tasks in this order:

1. `summarizeStats`
2. `removeOrphanedFeaturedMedia`
3. `removeOrphanedContentMedia`
4. `removeOrphanedBlockMedia`
5. `removeOldSessionFiles`
6. `publishScheduledContent`
7. `autoFeaturePosts`
8. `clearCache`, only when an earlier task requested it
9. `generateSitemap`
10. `checkRobotsTxt`
11. `syncGitHubRepositories`, only when a GitHub owner is configured

## Task behavior

### Analytics summary

Consolidates completed daily visitor data and published-post counts into `stats_summary`. The dashboard uses summaries for historical reporting while the current day remains available from live visitor records.

### Orphaned media cleanup

Reconciles featured, inline, and block media with their content references. It removes the managed files and stale media records that no longer have a valid owner.

### Session cleanup

Removes expired file-based session data from the configured session path.

### Scheduled publication

Publishes non-deleted posts and pages whose status is `scheduled`, whose publication time is present, and whose UTC time has arrived.

### Automatic featuring

When enabled, recalculates featured public posts. Eligible posts must be published, listed, not pending review, and have a valid publication time. Member-only content is excluded.

### Cache clear

Runs only when a task reports a change that requires cached public output to be refreshed. Operators can also clear the cache directly from the dashboard.

### Search visibility files

Regenerates `sitemap.xml` and `sitemap.xml.gz`, then checks the required `robots.txt` content. Generated files require write access in `public/`.

### GitHub synchronization

When the active brand has a GitHub owner, retrieves public repository metadata and synchronizes the local `github` table. Without an owner, the task is omitted rather than recorded as a no-op.

## Failure handling

Each task runs independently. An exception or `false` result marks that task as failed, logs the error, and allows later tasks to continue.

The final status is:

| Status | Meaning |
| --- | --- |
| `success` | No task failed |
| `partial` | At least one task succeeded and at least one failed |
| `failed` | Every attempted task failed |

Every completed run is saved with its trigger, result list, start time, and finish time. Triggers are `cli` or `admin`.

## Monitoring

Monitor both process execution and the application's `cron_runs` table. A scheduler can launch successfully while an individual task records a partial result.

Alert when:

- no run appears within the expected daily window;
- the latest run is `partial` or `failed`;
- sitemap or robots files stop updating;
- scheduled content remains overdue;
- media cleanup repeatedly logs filesystem failures;
- GitHub synchronization fails repeatedly on a deployment that uses it.

## Safe manual run

Before running maintenance manually on production, confirm that no scheduled invocation is already active and that a recent database and media backup exists. After the command, inspect the latest cron record and the application logs rather than relying only on the command exit experience.
