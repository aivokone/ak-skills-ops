## Local Development Setup

Use DDEV as the default local runtime for Seravo template projects.

## Why DDEV-First

- Better compatibility on Apple Silicon (ARM)
- Avoids legacy local container helper assumptions (`vagrant` user, TTY-required
  `docker exec -it`, container key-copy steps)
- Predictable agent/CI behavior with host-side SSH and DDEV commands

## Prerequisites

- SSH access to Seravo host (public key already installed)
- Homebrew
- DDEV installed

```bash
brew install ddev/ddev/ddev
mkcert -install
```

- Colima or Docker Desktop running

## Bootstrap Steps

### 1) Clone project

```bash
ssh-keyscan -4 -p PORT HOST >> ~/.ssh/known_hosts
git clone --origin production ssh://USER@HOST:PORT/data/wordpress .
```

If directory is not empty:

```bash
git init
git remote add production ssh://USER@HOST:PORT/data/wordpress
git fetch production
git checkout -b main production/master
```

### 2) Composer compatibility upgrade (older templates)

Update `composer.json` constraints:

- `johnpbloch/wordpress-core-installer`: `^2.0`
- `johnpbloch/wordpress-core`: `^5.0 || ^6.0`
- `composer/installers`: `^1.0 || ^2.0`
- `vlucas/phpdotenv`: `^2.4 || ^5.0`

Then:

```bash
composer update --no-interaction
test -d htdocs/wordpress && echo OK
```

### 3) Configure DDEV

```bash
ddev config --project-type=wordpress --docroot=htdocs --project-name=SLUG --php-version=8.2
```

### 4) Patch `wp-config.php`

Add DDEV include before DB constants:

```php
$ddev_settings = dirname(__FILE__) . '/wp-config-ddev.php';
if (is_readable($ddev_settings) && !defined('DB_USER')) {
  require_once($ddev_settings);
}
```

Guard Seravo DB constants:

```php
if (!defined('DB_NAME')) { define('DB_NAME', getenv('DB_NAME')); }
if (!defined('DB_USER')) { define('DB_USER', getenv('DB_USER')); }
if (!defined('DB_PASSWORD')) { define('DB_PASSWORD', getenv('DB_PASSWORD')); }
if (!defined('DB_HOST')) { define('DB_HOST', getenv('DB_HOST')); }
```

Dotenv API in modern setups:

```php
$dotenv = Dotenv\Dotenv::createMutable($root_dir);
$dotenv->load();
```

### 5) Disable Redis object cache drop-in locally

```bash
mv htdocs/wp-content/object-cache.php htdocs/wp-content/object-cache.php.seravo-bak
```

### 6) Start local runtime

```bash
ddev start
```

### 7) Verify local WordPress path

In Seravo template layout, use explicit WP path in DDEV:

```bash
ddev wp option get siteurl --path=/var/www/html/htdocs/wordpress
ddev wp plugin list --path=/var/www/html/htdocs/wordpress
```

## Uploads Strategy

Treat uploads as a separate last step. Ask user to choose one option:

1. Full uploads rsync
2. Proxy missing uploads to production (recommended default)
3. Skip uploads

Do not run uploads sync by default in early setup steps.

## Legacy Notes

`docker-compose up`, `wp-development-up`, and `wp-pull-production-*` local helper
commands are legacy patterns and should not be the primary recommendation.
