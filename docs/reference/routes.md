# Route reference

Routes are declared explicitly in `app/Config/Routes.php`. The tables below describe version `4.0.1` at a documentation level and are not a replacement for `php spark routes` during deployment checks.

## Public and authentication routes

| Method | Path | Behavior | Condition |
| --- | --- | --- | --- |
| GET | `/` | Home post index | Always available |
| GET | `/blog` | Separate blog index | Redirects home when blog is disabled |
| GET | `/archive` | Date archive and search | Registered only when archive is enabled |
| GET, HEAD | `/feed` | RSS 2.0 feed | Registered only when RSS is enabled |
| GET | `/post/<slug>` | One published visible post | Always declared |
| GET | `/topic/<slug>` | Published posts for one topic | Always declared |
| GET | `/topic-group/<slug>` | Published posts for one topic group | Always declared |
| GET | `/<page-path>` | Hierarchical published page | Catch-all, declared last |
| GET, POST | `/login` | Shield session login | Always available |
| GET | `/logout` | Shield logout | Always available |
| GET, POST | `/register` | Shield registration | Added only when registration is enabled |
| GET | `/activate` | Activation-code form | Pending authentication action |
| POST | `/activate/resend` | Resend existing activation code | Cooldown applies |
| POST | `/activate/verify` | Verify activation code | Pending authentication action |
| GET | `/activate/success` | Activation completion page | Requires successful activation flow |

The custom 404 controller handles unresolved public content. Public content queries also require published status and audience visibility.

## CLI route

| Method | Path | Controller | Purpose |
| --- | --- | --- | --- |
| CLI | `cron` | `System\Cron::run` | Run scheduled maintenance |

Invoke it from the application root with `php public/index.php cron`.

## Admin route boundary

Every route below `/admin` requires both:

- an authenticated Shield session;
- `dashboard.access`.

Additional permissions are shown below.

### Dashboard pages

| Method | Path | Permission |
| --- | --- | --- |
| GET | `/admin/` | Base admin boundary |
| GET | `/admin/dashboard/` | Base admin boundary |
| GET | `/admin/dashboard/overview` | `admin.access` |
| GET | `/admin/dashboard/posts` | Any post create or edit permission |
| GET | `/admin/dashboard/pages` | `pages.manage` |
| GET | `/admin/dashboard/topics` | `topics.manage` |
| GET | `/admin/dashboard/blocks` | `blocks.manage` |
| GET | `/admin/dashboard/media` | `media.manage` |
| GET | `/admin/dashboard/trash` | `posts.delete` or `pages.manage` |
| GET | `/admin/dashboard/users` | `users.manage` |
| GET | `/admin/dashboard/cron-jobs` | `system.manage` |
| GET | `/admin/dashboard/analytics` | `system.manage` |
| GET | `/admin/dashboard/analytics/chart` | `system.manage` |
| GET | `/admin/dashboard/error-logs` | `system.manage` |
| GET | `/admin/dashboard/system-info` | `system.manage` |
| POST | `/admin/dashboard/actions` | `system.manage` |

Supported dashboard actions are cache clear, immediate cron execution, and error-log clear.

### Posts and pages

| Method | Path | Permission |
| --- | --- | --- |
| GET | `/admin/edit/post/new` | `posts.create` |
| POST | `/admin/edit/post/new/save` | `posts.create` |
| GET | `/admin/edit/post/<id>` | `posts.edit-own` or `posts.edit-any` |
| POST | `/admin/edit/post/<id>/save` | `posts.edit-own` or `posts.edit-any` |
| POST | `/admin/edit/post/<id>/republish` | `posts.edit-own` or `posts.edit-any` |
| POST | `/admin/edit/post/<id>/trash` | `posts.delete` |
| GET | `/admin/edit/page/new` | `pages.manage` |
| POST | `/admin/edit/page/new/save` | `pages.manage` |
| GET | `/admin/edit/page/<id>` | `pages.manage` |
| POST | `/admin/edit/page/<id>/save` | `pages.manage` |
| POST | `/admin/edit/page/<id>/trash` | `pages.manage` |
| POST | `/admin/slug/generate` | Post create/edit or topic management |

Trash restore and permanent-delete routes are separate POST endpoints below `/admin/dashboard/trash/<type>/<id>/...` and require the matching post or page permission.

### Media

| Method | Path | Permission |
| --- | --- | --- |
| POST | `/admin/media/featured-post-image` | `media.upload` |
| POST | `/admin/media/featured-page-image` | `pages.manage` |
| POST | `/admin/media/content-image` | `media.upload` |
| POST | `/admin/media/block-image` | `blocks.manage` |

Each endpoint fixes the media collection on the server. The client does not choose an arbitrary collection.

### Structured data management

Blocks, block groups, topics, topic groups, and users follow the same route pattern:

| Entity | GET | POST save | POST delete | Permission |
| --- | --- | --- | --- | --- |
| Block | `/admin/block/<id>` | `/admin/block/save` | `/admin/block/delete` | `blocks.manage` |
| Block group | `/admin/block-group/<id>` | `/admin/block-group/save` | `/admin/block-group/delete` | `blocks.manage` |
| Topic | `/admin/topic/<id>` | `/admin/topic/save` | `/admin/topic/delete` | `topics.manage` |
| Topic group | `/admin/topic-group/<id>` | `/admin/topic-group/save` | `/admin/topic-group/delete` | `topics.manage` |
| User | `/admin/user/<id>` | `/admin/user/save` | `/admin/user/delete` | `users.manage` |

Create dialogs use an ID value understood by the corresponding controller and service. Save and delete operations remain POST-only and CSRF-protected.

## Routing notes

- The page route must remain last.
- Route permissions should be updated together with `Config\AuthGroups` and service ownership checks.
- Feature-dependent routes are evaluated during route loading, so configuration cache and deployment restarts can affect when changes become visible.
- Run `php spark routes` after any route, environment, or feature change.
