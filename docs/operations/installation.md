# Requirements and installation

This guide is for authorized users of the private VerdinCore source. The public documentation repository is not an installable application.

## Requirements

- PHP `8.5.x`;
- Composer 2;
- MariaDB or a compatible MySQL server;
- Node.js and npm for asset builds;
- a web server that can use `public/` as its document root.

Required PHP functionality includes:

- `ctype`;
- `curl`;
- `dom`;
- `fileinfo`;
- `filter`;
- `gd`;
- `intl`;
- `mbstring`;
- `mysqli`;
- `zlib`.

The default image pipeline requires GD support for both `imageavif()` and `imagecreatefromavif()`.

## 1. Obtain the source

Clone or unpack the private source using the access method supplied by the copyright holder.

```bash
git clone <authorized-repository-url> verdincore
cd verdincore
```

Access to the repository does not change the terms in the proprietary license.

## 2. Install dependencies

```bash
composer install
npm ci
```

For a production installation, use the optimized Composer command in the [deployment guide](deployment.md) after testing the release.

## 3. Create `.env`

Copy the supplied `env` template and edit the new file.

```bash
cp env .env
```

PowerShell:

```powershell
Copy-Item env .env
```

At minimum, configure the environment, application URL, database, active brand, active theme, and email sender:

```dotenv
CI_ENVIRONMENT = development

app.baseURL = 'http://verdincore.test/'
app.defaultLocale = en
app.forceGlobalSecureRequests = false

database.default.database = 'verdincore'
database.default.username = 'verdincore'
database.default.password = 'change-me'

brand.name = verdincore
verdincore.theme = tomgineer

email.fromEmail = 'no-reply@example.com'
email.fromName = 'Example Brand'
email.SMTPHost = ''
email.SMTPPass = ''
```

Registration requires working email delivery for activation codes. Keep registration disabled until SMTP and activation have been tested.

## 4. Create the database

Create an empty database, then import the complete schema:

```bash
mysql -u verdincore -p verdincore < database/schema.sql
```

Version `4.0.1` installs from `database/schema.sql`. Application migrations are not the update mechanism. Import the schema only into a new database. For an existing deployment, take a backup and apply release-specific database changes deliberately.

## 5. Build assets

```bash
npm run build
```

The build reads `verdincore.theme` from `.env`, compiles the selected public theme, and also builds the administration bundles. Generated files are written below `public/assets/themes/`.

## 6. Set writable paths

The PHP process needs write access to:

- `writable/`;
- `public/images/`;
- `public/robots.txt`;
- `public/sitemap.xml`;
- `public/sitemap.xml.gz`.

Grant access to the web and scheduler account only. Avoid broad world-writable permissions.

## 7. Start locally

For a quick development check:

```bash
php spark serve
```

The framework server normally listens on `http://localhost:8080`. For regular development, configure a local virtual host whose document root points directly to `public/`.

## 8. Create the first administrator

Register and activate the first trusted user, then assign `superadmin` directly in the new database:

```sql
SELECT id, username FROM users;

UPDATE auth_groups_users
SET `group` = 'superadmin'
WHERE user_id = 1;
```

Replace `1` with the verified user ID. Disable public registration again if the site does not need ongoing self-service accounts.

## Installation check

Before adding content, confirm:

- the home page renders without a framework error;
- login and email activation work;
- the administrator can enter the dashboard;
- CSS, JavaScript, icons, and favicons load from `public/assets/themes/`;
- a test image uploads and produces its variants;
- a draft post saves and can be published;
- `php public/index.php cron` completes and records a run;
- `composer test` passes in the target PHP environment.
