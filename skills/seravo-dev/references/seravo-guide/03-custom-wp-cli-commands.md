## Custom WP-CLI Commands

Seravo provides extensive custom commands prefixed with `wp-`. These are available in production and local environments.

### Backup & Restore

**wp-backup**
- Dumps database and backs up /data directory using rdiff-backup
- Runs automatically every night
- Always run before major changes
```bash
wp-backup
# Output: Successfully backed up 323 MB of data
```

**wp-backup-status**
- Lists all backup increments by date
- Shows backup sizes
```bash
wp-backup-status
```

**wp-backup-list-changes**
- Lists files changed since last backup, sorted by date
- More reliable than checking mtime attributes
- Use to audit what actually changed
```bash
wp-backup-list-changes
```

**wp-backup-list-changes-since**
- Lists files changed since specific date
```bash
wp-backup-list-changes-since 2024-01-15
```

### Database Synchronization

**wp-pull-production-db**
- Copies production database to local Vagrant/Docker
- Automatically replaces production URLs with local URLs from config.yml
- Safe for development without affecting production
```bash
wp-pull-production-db
# Replaces example.com → example.local automatically
```

**wp-pull-shadow-db** / **wp-pull-staging-db**
- Pulls database from shadow (staging) environment
- Wrapper versions for different terminology
```bash
wp-pull-shadow-db
# OR
wp-pull-staging-db
```

**Database workflow context**: These commands handle URL replacement intelligently. For multisite, subdomains are automatically mapped (site1.example.com → site1.wordpress.local).

### Development Environment Setup

**wp-development-up**
- Starts Seravo development environment
- Reads config.yml and loads production database copy
- Pulls plugins/themes if configured
```bash
wp-development-up
```

**wp-pull-production-plugins**
- Uses rsync to sync plugins from production to local
- Makes local wp-content/plugins identical to production
```bash
wp-pull-production-plugins
```

**wp-pull-production-themes**
- Syncs themes from production
```bash
wp-pull-production-themes
```

**wp-pull-production-core**
- Installs same WordPress core version as production
```bash
wp-pull-production-core
```

### Cache Management

**wp-purge-cache**
- Purges ALL server caches:
  - Nginx proxy cache
  - WordPress object cache
  - WordPress rewrite cache
  - PageSpeed cache
- Run after database imports, configuration changes, or when debugging caching issues
```bash
wp-purge-cache
```

### Security & Access

**wp-reset-ssh-password**
- Only way to change SSH password for Seravo site
- Cannot be changed via other methods
```bash
wp-reset-ssh-password
```

**wp-reset-all-passwords**
- Resets passwords for all WordPress users
- Also resets sessions automatically
```bash
wp-reset-all-passwords
```

**wp-reset-all-sessions**
- Forces all users to log in again
- Useful after security incidents
```bash
wp-reset-all-sessions
```

**wp-check-haveibeenpwned**
- Checks if password hash appears in breach databases
- Uses haveibeenpwned.com API
```bash
wp-check-haveibeenpwned <hash>
```

### Monitoring & Debugging

**wp-last-ssh-logins**
- Lists recent SSH login attempts (successful and failed)
- Based on wtmp and btmp logs
```bash
wp-last-ssh-logins
```

**wp-last-wp-logins**
- Shows WordPress login history
- Includes failed login attempts
- Data from wp-login.log
```bash
wp-last-wp-logins
```

**wp-list-files-mtime** / **wp-list-files-ctime**
- Lists recently changed files (last 30 days)
- Less reliable than wp-backup-list-changes
- mtime = modification time, ctime = change time
```bash
wp-list-files-mtime
wp-list-files-ctime
```

### Performance Testing

**wp-speed-test**
- Measures WordPress (PHP) page load time
```bash
wp-speed-test
```

**wp-load-test**
- Measures consecutive PHP requests per minute
- Simple performance benchmark
```bash
wp-load-test
```

### WordPress Network (Multisite)

**wp-network-status**
- Shows WordPress Network status and configuration
- Displays if site is multisite and domain mappings
```bash
wp-network-status
```

**Multisite context**: For multisite, most wp commands need `--url=` parameter to target specific subsite. Use `wp site list` to see all sites, `wp super-admin list` for super admins.

### Internal/Automated Commands

These are used by Vagrantfile/Docker - not for manual use:
- `wp-vagrant-activation` - Restarts Nginx, configures hosts
- `wp-vagrant-dump-db` - Dumps DB on halt/destroy
- `wp-vagrant-import-db` - Imports DB on vagrant up
- `wp-activate-git-hooks` - Sets up test automation hooks
- `wp-generate-ssl` - Generates self-signed certs for local domains
- `wp-activate-asset-proxy` - Configures asset proxy from config.yml

### Plugin Management

**wp-seravo-plugin-update**
- Updates must-use Seravo Plugin
- Use `--dev` for latest git master (unstable)
```bash
wp-seravo-plugin-update
# OR for dev version:
wp-seravo-plugin-update --dev
```

### Environment Variables

**wp-list-env**
- Lists all environment variables
- Shows database credentials, salts, WP_ENV, etc.
- Essential for understanding configuration
```bash
wp-list-env
```

**Key variables**:
- `WP_ENV`: Environment name (production, staging, development)
- `CONTAINER`: Container instance name (changes over time, don't rely on it)
- `DEFAULT_DOMAIN`: Internal domain (e.g., example.fi.seravo.com)
- Database credentials: All via getenv() in wp-config.php
- Salts: AUTH_KEY, SECURE_AUTH_KEY, etc.
