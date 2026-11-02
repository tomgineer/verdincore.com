# Brands and themes

VerdinCore separates site identity from site presentation. A brand answers "whose site is this?" A theme answers "how is it rendered?"

## Select a brand and theme

Environment settings choose both independently:

```dotenv
brand.name = verdincore
verdincore.theme = tomgineer
```

`brand.name` resolves to `brands/<name>/config.php`. `verdincore.theme` resolves public views under `app/Views/<theme>/` and front-end sources under `themes/<theme>/`.

Version `4.0.1` registers these brand names:

- `portfolio`;
- `tomgineer`;
- `verdindynamics`;
- `verdincore`.

An unsupported name causes configuration loading to fail instead of silently falling back.

## Brand configuration

A brand file defines:

| Group | Values |
| --- | --- |
| Identity | Display name, slogan, description, default author, copyright, platform name |
| Site | Canonical domain and locale |
| Contact | Email and postal address fields |
| Schema | Person or Organization identity, URL, image, logo, and same-as links |
| Social | Facebook, Instagram, LinkedIn, X, TikTok, Telegram, Reddit, Discord, WhatsApp, YouTube, Rumble, BitChute, and GitHub links |
| Integration | Optional GitHub owner for public repository synchronization |

Brand assets can include a logo, icon, profile image, sharing image, and favicon set. The CSS and JavaScript build does not copy these files, so the release artifact must publish them to the matching public theme asset directory.

## Add a brand

1. Create `brands/<name>/config.php` using an existing brand as the shape reference.
2. Add the required assets below `brands/<name>/assets/`.
3. Register the exact name in `Config\Brand::SUPPORTED_BRANDS`.
4. Select it with `brand.name` in the target environment.
5. Publish the brand assets and run a production asset build.
6. Verify metadata, structured data, email sender identity, social links, favicons, and the optional GitHub panel.

Do not place secrets in the brand file. Credentials and environment-specific transport values belong in `.env` or the deployment's secret store.

## Theme structure

A public theme spans PHP views in `app/Views/<theme>/` plus CSS and JavaScript entry points in `themes/<theme>/`. Shared application cells render navigation, footers, archives, featured content, related content, and other reusable pieces through the selected view namespace.

The administration interface has its own theme sources and compiled bundles. Changing the public theme does not replace the admin UI.

## Feature configuration

Feature flags are deployment settings, not brand fields. They control registration, topics, blog navigation, archive and search, related posts, display flags, automatic featuring, footer pages, RSS, and caching.

Keep this distinction clear:

- brand configuration describes identity;
- VerdinCore configuration controls behavior;
- theme code controls presentation.

## Structured identity

When a brand defines a Schema.org Person or Organization, public structured-data graphs reference it as the site publisher. About pages may use the identity as their main entity. Invalid or empty same-as URLs are filtered out.

Member-only pages and posts do not expose structured identity through a page graph because structured data is omitted for those responses.
