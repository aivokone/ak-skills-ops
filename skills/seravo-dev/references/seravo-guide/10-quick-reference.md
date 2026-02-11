## Quick Reference

### Essential Commands

**Daily workflows**:
```bash
# Backup before changes
wp-backup

# Pull latest from production
wp-pull-production-db

# Clear all caches
wp-purge-cache

# Check environment
wp-list-env
```

**Deployment**:
```bash
git add .
git commit -m "Description"
git push production master
```

**Troubleshooting**:
```bash
wp-last-ssh-logins    # Check access
wp-last-wp-logins     # Check WP logins
wp-backup-list-changes # Check file changes
wp-speed-test         # Check performance
```

### Directory Quick Reference

```
/data/wordpress/htdocs/           → Web root
/data/wordpress/htdocs/wordpress/ → WP core (don't edit)
/data/wordpress/htdocs/wp-content/→ Your content (version control this)
/data/backups/                    → Automated backups
/data/log/                        → Log files
/data/db/                         → Database exports
~/.ssh/                           → SSH configuration
```

### Configuration Files

```
config.yml           → Local dev environment config
docker-compose.yml   → Docker container definition
.env                 → Local environment overrides (gitignored)
wp-config.php        → WordPress config (uses getenv())
nginx/*.conf         → Custom Nginx configuration
```

### Common SSH Targets

```bash
# Production
ssh user@example.seravo.com -p 12345

# Shadow/staging
ssh user@example.seravo.com -p 23456

# Local Docker
ssh wordpress.local -F .vagrant/ssh/config
```

### Seravo SSH First-Connect (IPv4-Safe)

```bash
# Add host key before ssh-copy-id / first login
ssh-keyscan -4 -p 12345 example.seravo.com >> ~/.ssh/known_hosts

# Test with IPv4 forced
ssh -4 -p 12345 user@example.seravo.com 'echo OK'
```
