## Troubleshooting and Performance

### Common Issues

### 1) SSH access fails (`Permission denied`, host key errors)

Use IPv4-safe onboarding:

```bash
ssh-keyscan -4 -p PORT HOST >> ~/.ssh/known_hosts
ssh-copy-id -o AddressFamily=inet -p PORT USER@HOST
ssh -4 -p PORT USER@HOST 'echo OK'
```

If `ssh-copy-id` is non-interactive, use `sshpass` fallback.

### 2) Apple Silicon local runtime failures with Seravo legacy image

Symptoms can include container crashes, QEMU instability, disk lock errors, and
TTY issues in agent shells. Preferred fix:

- Stop using Seravo local Docker/Vagrant helper flow as primary local path.
- Use DDEV workflow (`ddev config`, `ddev start`, host-side SSH export, direct rsync).

### 3) DDEV WP-CLI cannot find WordPress

In Seravo template layout, always pass path in DDEV:

```bash
ddev wp plugin list --path=/var/www/html/htdocs/wordpress
```

### 4) `RedisException` in local CLI or requests

Seravo `object-cache.php` expects Redis integration.
Disable drop-in locally if Redis is not configured:

```bash
mv htdocs/wp-content/object-cache.php htdocs/wp-content/object-cache.php.seravo-bak
```

### 5) Composer dependency conflicts on old template snapshots

If Composer complains about plugin API/version constraints:

- Update Composer constraints for Composer 2 compatibility
- Run `composer update --no-interaction`
- Verify `htdocs/wordpress` exists

### 6) Dotenv runtime errors after dependency update

Use modern API in `wp-config.php`:

```php
$dotenv = Dotenv\Dotenv::createMutable($root_dir);
$dotenv->load();
```

### 7) Git push rejected

Common checks:

```bash
cat .git/hooks/pre-commit
cat .git/hooks/pre-push
php -l wp-content/themes/mytheme/*.php
```

For GitHub auth collisions with Seravo SSH settings, prefer HTTPS `origin` and
`gh auth setup-git`.

### 8) Cache issues on Seravo server

Use full Seravo purge:

```bash
wp-purge-cache
```

For local DDEV cache flush:

```bash
ddev wp cache flush --path=/var/www/html/htdocs/wordpress
```

### 9) Multisite URL issues

Confirm `DOMAIN_CURRENT_SITE` behavior by environment and validate replacements
for each subsite (`--url=`) when debugging.

## Performance Optimization

### Server-side quick checks

```bash
wp-speed-test
wp-load-test
wp db optimize
```

### Logs

```bash
tail -f /data/log/php-error.log
tail -f /data/log/nginx-access.log
tail -f /data/log/wordpress.log
```

### Database and health diagnostics

```bash
wp db query "SHOW FULL PROCESSLIST;"
wp site health status
wp cron event list
```

### Plugin conflict isolation

```bash
wp plugin deactivate --all
wp plugin activate plugin-name
```

For must-use plugin incidents, inspect `wp-content/mu-plugins/` directly.
