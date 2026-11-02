# Configuration

VerdinCore combines environment settings, PHP configuration classes, and per-brand files. Secrets belong in the deployment environment, not in a brand or theme.

## Environment groups

| Prefix | Purpose |
| --- | --- |
| `app.*` | Base URL, locale, and HTTPS behavior |
| `database.default.*` | MariaDB or MySQL connection |
| `email.*` | Activation email sender and SMTP transport |
| `cookie.*` | Cookie security |
| `brand.*` | Active brand selection |
| `verdincore.*` | Theme, features, limits, cache, and editorial behavior |
| `images.*` | Image output quality |

The supplied `env` file is the current setting template. Copy it to `.env` and keep `.env` out of version control.

## Application settings

```dotenv
CI_ENVIRONMENT = production
app.baseURL = 'https://example.com/'
app.defaultLocale = en
app.forceGlobalSecureRequests = true
cookie.secure = true
```

Use the canonical HTTPS URL, including the trailing slash. Production should force secure requests and use secure cookies. Confirm reverse-proxy and trusted-host behavior for the actual infrastructure.

## Brand and theme

```dotenv
brand.name = verdincore
verdincore.theme = tomgineer
```

The brand must be registered by the application. The theme name must match available public views and source assets. See [Brands and themes](../guides/brands-and-themes.md).

## Public features

```dotenv
verdincore.allowRegistration = false
verdincore.topics = true
verdincore.blog = false
verdincore.archive = false
verdincore.footer.pages = true
```

| Setting | Effect |
| --- | --- |
| `allowRegistration` | Adds or removes the public registration route |
| `topics` | Displays topic data in public presentation |
| `blog` | Enables the separate `/blog` index; otherwise it redirects home |
| `archive` | Adds `/archive`, date filtering, and search |
| `footer.pages` | Shows eligible featured pages in the footer |

Topic and topic-group routes remain declared, while the `topics` flag controls their use in site presentation and post data.

## Content lists and sidebars

```dotenv
verdincore.posts.itemsPerPage = 10
verdincore.sidebar.itemsPerSection = 10
verdincore.dashboard.itemsPerPage = 10
verdincore.dashboard.analyticsChartDays = 30
```

Use positive, conservative values. Large pages increase query, rendering, and response cost.

## Post behavior

```dotenv
verdincore.post.related.enabled = true
verdincore.post.related.limit = 6
verdincore.post.flags = true
verdincore.post.minViewsToShow = 50
verdincore.post.autoFeature = false
verdincore.post.autoFeatureRatio = 15
verdincore.post.autoFeatureThreshold = 70
```

Related posts are selected from shared topics. The flag setting controls visible labels such as featured and video. The minimum-views setting prevents very small counts from being displayed.

Automatic featuring runs during cron. The ratio controls featured capacity relative to the eligible cohort, and the threshold controls the promotion score. Test changes against representative content before enabling it.

## Editorial settings

```dotenv
verdincore.content.allowAuthorOverride = false
```

When enabled, the editor can select another non-deleted user as the author. Route permissions still govern who can edit the content.

## RSS

```dotenv
verdincore.rss.enabled = false
verdincore.rss.itemsPerFeed = 20
verdincore.rss.includeContent = false
```

Enabling RSS adds GET and HEAD handling for `/feed`. The feed contains published public posts. Full content can be included with `includeContent`, otherwise items use their excerpt when available.

## Page cache

```dotenv
verdincore.cache.enabled = true
verdincore.cache.duration = 3600
```

Full-page caching is effective only in `production`. It applies to guest GET and HEAD requests outside `/admin`, and only when the controller marks a response as cacheable. Authenticated requests bypass both cache reads and writes.

## Images

```dotenv
images.quality = 55
```

The environment overrides the image quality used when saving generated files. Collection dimensions and accepted original formats are defined in `Config\Images` and are not exposed as environment settings.

## Email

```dotenv
email.fromEmail = 'no-reply@example.com'
email.fromName = 'Example Brand'
email.SMTPHost = 'smtp.example.com'
email.SMTPPass = 'secret-from-a-secure-store'
```

Configure the remaining CodeIgniter email settings required by the provider. Test activation delivery, resend behavior, sender alignment, and bounce handling before allowing registration.

## Configuration checklist

- No example password remains.
- `.env` is excluded from Git and protected by filesystem permissions.
- The base URL, brand domain, and HTTPS behavior agree.
- The selected brand and theme exist.
- Registration is disabled unless email activation is operational.
- Member-only pages return private cache headers.
- Page caching has been tested with both guest and logged-in requests.
- Cron uses the same environment and PHP version as the web application.
