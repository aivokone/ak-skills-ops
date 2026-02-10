## Troubleshooting & Performance

### Common Issues

**1. "Permission denied" errors**

Check SSH key setup:
```bash
# Generate key if needed
ssh-keygen -t rsa -b 4096

# Copy to Seravo
ssh-copy-id -p 12345 user@example.seravo.com

# Test connection
ssh user@example.seravo.com -p 12345
```

Windows users: Manually copy .pub key to `~/.ssh/authorized_keys` on server.

**2. Local site not accessible (Docker)**

Check container status:
```bash
docker-compose ps

# If not running:
docker-compose up -d

# Check logs:
docker-compose logs
```

Verify domains in config.yml and hosts file:
```bash
# On macOS/Linux
cat /etc/hosts | grep wordpress.local

# Should see container IP → domain mapping
```

If using .local domains, ensure avahi: true in config.yml.

**3. Database import fails**

Remove ENGINE definitions:
```bash
# Edit SQL file to remove ENGINE=InnoDB or ENGINE=MyISAM
sed -i 's/ENGINE=InnoDB//g' database.sql
sed -i 's/ENGINE=MyISAM//g' database.sql

# Then import
wp db import database.sql --skip-optimization
```

**4. Asset proxy not working**

Verify configuration:
```bash
# Check config.yml has production domain
cat config.yml | grep domain

# Manually activate
wp-activate-asset-proxy

# Test internet connectivity from container
ping -c 3 google.com
```

**5. Git push rejected**

Check for test failures:
```bash
# View git hooks
cat .git/hooks/pre-commit
cat .git/hooks/pre-push

# Test manually
php -l wp-content/themes/mytheme/*.php

# Bypass (use sparingly)
git push production master --no-verify
```

**6. Nginx 403 errors on Adminer**

Enable Adminer feature:
```bash
srv feature adminer --status
srv feature adminer --enable
```

**7. Cache not clearing**

Nuclear option:
```bash
wp-purge-cache
# Purges: Nginx proxy, object cache, rewrite cache, PageSpeed

# Verify
curl -I https://example.com | grep -i cache
```

**8. Multisite URL issues**

For networks, verify DOMAIN_CURRENT_SITE:
```bash
# Check environment
wp-list-env | grep DOMAIN

# In wp-config.php, should see:
if ('development' === getenv('WP_ENV')) {
    define('DOMAIN_CURRENT_SITE', 'example.local');
} else {
    define('DOMAIN_CURRENT_SITE', 'example.com');
}
```

### Performance Optimization

**1. Identify slow queries**
```bash
wp db query "SHOW FULL PROCESSLIST;"

# Check slow query log (if enabled)
tail -f /data/log/mysql-slow.log
```

**2. Benchmark site performance**
```bash
# PHP page load time
wp-speed-test

# Load testing
wp-load-test

# Check object cache status
wp cache info  # If Redis/Memcached enabled
```

**3. Database optimization**
```bash
# Optimize all tables
wp db optimize

# Clean up
wp transient delete --all
wp post delete $(wp post list --post_status=trash --format=ids)
```

**4. Monitor resource usage**

Check logs:
```bash
# Error log
tail -f /data/log/php-error.log

# Access log
tail -f /data/log/nginx-access.log

# Application log
tail -f /data/log/wordpress.log
```

**5. Nginx configuration**

Seravo manages Nginx. For custom configuration:
- Create `/data/wordpress/nginx/*.conf` files
- They're automatically included
- Test with: `nginx -t`
- Reload: `systemctl reload nginx` (via support if needed)

### Advanced Diagnostics

**Check site health**:
```bash
wp site health status
```

**Verify cron**:
```bash
wp cron event list
wp cron test
```

**Plugin conflicts**:
```bash
# Deactivate all
wp plugin deactivate --all

# Activate one by one to identify culprit
wp plugin activate plugin-name
```

**Must-use plugins** (in wp-content/mu-plugins/):
- Auto-activated, can't be disabled via wp-cli
- Remove with: `rm -rf wp-content/mu-plugins/plugin-name`
- Always backup first: `wp-backup`
