## Quick Reference

### Essential Commands

**Daily workflows**:

```bash
# Backup before production changes
wp-backup

# Start local environment
ddev start

# Pull production DB to local (recommended)
ssh -4 -p PORT USER@HOST 'wp db export -' | gzip > /tmp/production.sql.gz
ddev import-db --file=/tmp/production.sql.gz

# Local URL rewrite + cache flush
DDEV_PATH=/var/www/html/htdocs/wordpress
ddev wp search-replace 'https://production-url' 'https://SLUG.ddev.site' --all-tables --path="$DDEV_PATH"
ddev wp cache flush --path="$DDEV_PATH"
```

**Deployment**:

```bash
git add .
git commit -m "Description"
git push production master
```

**Troubleshooting**:

```bash
wp-last-ssh-logins      # SSH access history
wp-last-wp-logins       # WP login history
wp-backup-list-changes  # Changed files since backup
wp-speed-test           # Performance baseline
```

### Directory Quick Reference

```text
/data/wordpress/htdocs/            -> Web root
/data/wordpress/htdocs/wordpress/  -> WP core (do not edit directly)
/data/wordpress/htdocs/wp-content/ -> Site code/content
/data/backups/                     -> Automated backups
/data/log/                         -> Logs
/data/db/                          -> DB exports
~/.ssh/                            -> SSH keys/config
```

### Configuration Files

```text
.ddev/config.yaml                  -> DDEV project config
.ddev/nginx_full/uploads-proxy.conf -> Optional uploads proxy
.env                               -> Local environment overrides (gitignored)
wp-config.php                      -> WordPress config and env bootstrap
nginx/*.conf                       -> Custom Nginx config on Seravo
```

### Common SSH Targets

```bash
# Production
ssh user@example.seravo.com -p 12345

# Shadow/staging
ssh user@example.seravo.com -p 23456
```

### Seravo SSH First-Connect (IPv4-safe)

```bash
ssh-keyscan -4 -p 12345 example.seravo.com >> ~/.ssh/known_hosts
ssh -4 -p 12345 user@example.seravo.com 'echo OK'
```

### Required DDEV WP-CLI Path

```bash
ddev wp plugin list --path=/var/www/html/htdocs/wordpress
```
