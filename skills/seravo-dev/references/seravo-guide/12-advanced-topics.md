## Advanced Topics

### Custom Nginx Configuration

Place files in `/data/wordpress/nginx/*.conf`:

Example - Force HTTPS redirect:
```nginx
# /data/wordpress/nginx/force-https.conf
if ($scheme != "https") {
    return 301 https://$host$request_uri;
}
```

Example - Custom headers:
```nginx
# /data/wordpress/nginx/security-headers.conf
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
```

Test configuration:
```bash
nginx -t
# If valid, Nginx will reload automatically
```

### Composer Dependency Management

Seravo supports Composer for plugin/theme management:

```bash
# Install dependency
composer require wpackagist-plugin/query-monitor

# Update dependencies
composer update

# Install from composer.json
composer install --no-dev
```

composer.json location: `/data/wordpress/composer.json`

### Environment-Specific Code

Use WP_ENV for conditional logic:

```php
// wp-config.php or functions.php
if ('production' === getenv('WP_ENV')) {
    // Production-only code
    define('WP_DEBUG', false);
} elseif ('development' === getenv('WP_ENV')) {
    // Development-only code
    define('WP_DEBUG', true);
    define('WP_DEBUG_LOG', true);
} elseif ('staging' === getenv('WP_ENV')) {
    // Staging-only code
    define('WP_DEBUG', false);
}
```

### Automated Testing Integration

Git hooks enable automated testing:

```bash
# Activate hooks
wp-activate-git-hooks

# Add custom tests to .git/hooks/pre-commit
# Example: PHP syntax check
find wp-content -name "*.php" -exec php -l {} \;
```

### Shadow Environment Management

**Purpose**: Test updates, plugins, major changes before production

**Access**:
```bash
# SSH to shadow (different port)
ssh user@example.seravo.com -p 23456

# Pull from production
wp-pull-production-db
wp-pull-production-plugins
wp-pull-production-themes

# Make changes, test
# If successful, apply same changes to production
```

**Restore production to shadow**:
- Follow "Restore Site from a Backup into Shadow" workflow
- Useful for testing with real production data

### WordPress Network Development

**Extra commands for multisite**:
```bash
# List all sites
wp site list

# List super admins
wp super-admin list

# Target specific subsite
wp plugin list --url=https://example.com/subsite

# Network-wide operations
wp user list --network
wp plugin list --network
```

**Local multisite setup**:
```yaml
# config.yml
development:
  domains:
    - wordpress.local
    - site1.wordpress.local
    - site2.wordpress.local
```

```php
// wp-config.php
if ('development' === getenv('WP_ENV')) {
    define('DOMAIN_CURRENT_SITE', getenv('DEFAULT_DOMAIN'));
}
```

**Database considerations**:
- Shared tables: wp_users, wp_usermeta
- Site-specific: wp_2_posts, wp_3_posts, etc.
- List all: `wp db tables --all-tables`
