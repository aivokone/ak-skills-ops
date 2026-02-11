# Seravo -> Local (DDEV) -> GitHub Private: Import Recipe

This is the recommended end-to-end workflow for Seravo template projects.
It avoids Seravo local Docker/Vagrant assumptions and works reliably on Apple
Silicon and non-interactive agent environments.

## Preconditions

- SSH key is already added to the target Seravo environment.
- Homebrew is available.
- DDEV is installed:

```bash
brew install ddev/ddev/ddev
mkcert -install
```

- Colima or Docker Desktop is running.
- If pushing to GitHub, `gh auth status` should work.

## 1) Clone From Seravo (IPv4-safe)

```bash
ssh-keyscan -4 -p PORT HOST >> ~/.ssh/known_hosts

git clone --origin production ssh://USER@HOST:PORT/data/wordpress .
```

If target directory is not empty (for example it already contains `.claude/`):

```bash
git init
git remote add production ssh://USER@HOST:PORT/data/wordpress
git fetch production
git checkout -b main production/master
```

## 2) Migrate Composer Constraints (Composer 1 -> 2)

Older Seravo template snapshots may pin Composer-1-only constraints.
Update `composer.json` dependencies to include Composer 2-compatible ranges:

```json
{
  "require": {
    "johnpbloch/wordpress-core-installer": "^2.0",
    "johnpbloch/wordpress-core": "^5.0 || ^6.0",
    "composer/installers": "^1.0 || ^2.0",
    "vlucas/phpdotenv": "^2.4 || ^5.0"
  }
}
```

Then run:

```bash
composer update --no-interaction
test -d htdocs/wordpress && echo OK
```

Use `composer update` here, not `composer install`, because lock files from older
templates are commonly outdated.

## 3) Configure DDEV

```bash
ddev config --project-type=wordpress --docroot=htdocs --project-name=SLUG --php-version=8.2
```

## 4) Patch `wp-config.php` for DDEV + Seravo Compatibility

Update `htdocs/wp-config.php`.

Add DDEV include before DB settings:

```php
$ddev_settings = dirname(__FILE__) . '/wp-config-ddev.php';
if (is_readable($ddev_settings) && !defined('DB_USER')) {
  require_once($ddev_settings);
}
```

Wrap Seravo DB constants with `!defined()` guards:

```php
if (!defined('DB_NAME')) { define('DB_NAME', getenv('DB_NAME')); }
if (!defined('DB_USER')) { define('DB_USER', getenv('DB_USER')); }
if (!defined('DB_PASSWORD')) { define('DB_PASSWORD', getenv('DB_PASSWORD')); }
if (!defined('DB_HOST')) { define('DB_HOST', getenv('DB_HOST')); }
```

Dotenv compatibility update for modern setups:

```php
$dotenv = Dotenv\Dotenv::createMutable($root_dir);
$dotenv->load();
```

Why this matters: Seravo template patterns often call `getenv()` directly; without
guards, DDEV definitions are not honored correctly in local runtime.

## 5) Disable Local Redis Object Cache Drop-in

Seravo `object-cache.php` expects Redis availability. DDEV defaults may not have
matching Redis wiring, causing WP-CLI failures.

```bash
mv htdocs/wp-content/object-cache.php htdocs/wp-content/object-cache.php.seravo-bak
```

## 6) Start Local Environment

```bash
ddev start
```

## 7) Import Production Database Without Seravo Local Container

```bash
ssh -4 -p PORT USER@HOST 'wp db export -' | gzip > /tmp/production.sql.gz
ddev import-db --file=/tmp/production.sql.gz
```

URL rewrite and cache flush (path is required in Seravo template layout):

```bash
ddev wp search-replace 'https://production-url' 'https://SLUG.ddev.site' \
  --all-tables --path=/var/www/html/htdocs/wordpress

ddev wp cache flush --path=/var/www/html/htdocs/wordpress
```

## 8) Sync Themes, Plugins, and MU Plugins Via Direct rsync

```bash
rsync -avz -e 'ssh -4 -p PORT' USER@HOST:/data/wordpress/htdocs/wp-content/themes/ htdocs/wp-content/themes/
rsync -avz -e 'ssh -4 -p PORT' USER@HOST:/data/wordpress/htdocs/wp-content/plugins/ htdocs/wp-content/plugins/
rsync -avz -e 'ssh -4 -p PORT' USER@HOST:/data/wordpress/htdocs/wp-content/mu-plugins/ htdocs/wp-content/mu-plugins/
```

## 9) Uploads Strategy (Run Last)

Always decide uploads strategy explicitly before executing this step.
Do this after DB + code/content sync to avoid long-running interruptions.

Options:

1. Full rsync of uploads (slow, large disk usage)
2. Proxy uploads to production (recommended default)
3. Skip uploads (site works but images may be missing)

Recommended proxy example:

```nginx
# .ddev/nginx_full/uploads-proxy.conf
location ~* /wp-content/uploads/ {
    try_files $uri @production;
}
location @production {
    proxy_pass https://PRODUCTION-URL;
}
```

## 10) Optional GitHub Private Push (Avoid SSH Key Collisions)

If repo-level `core.sshCommand` is set for Seravo, GitHub SSH pushes may fail.
Prefer HTTPS `origin` with `gh auth setup-git`.

```bash
gh auth setup-git
git remote add origin https://github.com/OWNER/REPO.git
git branch -M main
git add -A
git commit -m "Initial import from Seravo"
git push -u origin main
```

## 11) Verification Checklist

```bash
ddev wp option get siteurl --path=/var/www/html/htdocs/wordpress
ddev wp option get home --path=/var/www/html/htdocs/wordpress
ddev wp plugin list --path=/var/www/html/htdocs/wordpress
```

If object cache needs to be restored later, move the backup file back only when
Redis integration is intentionally configured for local environment.
