# Front-end build

VerdinCore has separate source and output paths for the selected public theme and the administration interface.

## Toolchain

- Tailwind CSS 4 compiles styles.
- daisyUI 5 supplies component styling.
- esbuild bundles browser JavaScript.
- Tiptap 3 powers the editors.
- Chart.js 4 powers dashboard charts.
- highlight.js supports code presentation on public content pages.

## Commands

| Command | Result |
| --- | --- |
| `npm run dev` | Watch public-theme and admin CSS and JavaScript |
| `npm run dev:theme` | Watch only the selected public theme |
| `npm run build` | Minify public-theme and admin production bundles |
| `npm run build:theme` | Minify only the selected public theme bundles |

The scripts read `verdincore.theme` from `.env`. If it is missing, the build configuration defaults to `tomgineer`.

## Public-theme outputs

For a selected theme named `<theme>`, production output includes:

```text
public/assets/themes/<theme>/css/app-dist.css
public/assets/themes/<theme>/js/app-dist.js
public/assets/themes/<theme>/js/highlightjs-dist.js
```

Source entry points live below `themes/<theme>/css/` and `themes/<theme>/js/`.

## Admin outputs

```text
public/assets/themes/admin/css/admin-dist.css
public/assets/themes/admin/js/admin-dist.js
public/assets/themes/admin/js/chartjs-dist.js
public/assets/themes/admin/js/tiptap-dist.js
```

The Chart.js and Tiptap vendor bundles are exposed as IIFE globals for the existing administration scripts.

## Static assets

Images, fonts, and favicons are served from their published directories below `public/assets/themes/`. The current JavaScript build script compiles CSS and JavaScript only. Static brand and theme assets must already be present in the deployment artifact or be published by the release process.

When adding a theme, verify:

- font paths from compiled CSS;
- logo, icon, profile, and sharing image paths;
- favicon links;
- `highlightjs-dist.js` on pages containing code blocks;
- login and activation views under the selected view namespace;
- responsive behavior in public and member sessions.

## Development workflow

1. Set the intended theme in `.env`.
2. Run `npm ci` after lockfile changes or on a clean checkout.
3. Start `npm run dev` or `npm run dev:theme`.
4. Exercise the affected PHP views and browser interactions.
5. Run `npm run build` before release.
6. Confirm that generated output belongs to the intended theme and that no stale bundle is being served from cache.

## Cache busting

Layouts append `APP_VERSION` to core CSS and JavaScript URLs. Update `VERSION` as part of the release process so browsers receive changed bundles. Asset versioning does not replace clearing server-side full-page and opcode caches after a deployment.
