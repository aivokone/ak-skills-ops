## Database Management Workflows

### Production → Local

**Standard workflow**:
```bash
# SSH into local Docker/Vagrant
ssh wordpress.local -F .vagrant/ssh/config

# Pull production database
wp-pull-production-db
# Automatically:
# - Exports production DB
# - Imports to local
# - Replaces URLs (example.com → example.local)
# - Handles multisite subdomains

# Clear caches
wp-purge-cache

# Verify
wp option get siteurl
wp option get home
```

### Local → Production (DANGEROUS)

**Warning**: Rarely needed. Usually only for initial deployment or emergency recovery.

```bash
# On local machine
wp db export local-db.sql

# Copy to production
scp -P 12345 local-db.sql user@example.seravo.com:/data/db/

# SSH to production
ssh user@example.seravo.com -p 12345

# BACKUP FIRST!
wp-backup

# Reset and import
cd /data/wordpress/htdocs
wp db reset --yes
wp db import /data/db/local-db.sql --skip-optimization

# Fix URLs
wp search-replace 'http://example.local' 'https://example.com' --all-tables

# Clear caches
wp-purge-cache
```

### Manual Database Export/Import

**Export**:
```bash
wp db export filename.sql
# Creates SQL dump with filename.sql
```

**Import**:
```bash
wp db import filename.sql
# Imports dump
# Use --skip-optimization for faster imports
```

**Database access**:
```bash
# Interactive MySQL shell (auto-authenticated)
wp db cli

# Get credentials
wp-list-env | grep DB_
```

### URL Search-Replace

**After database sync**:
```bash
# Replace URLs in database
wp search-replace 'old-url.com' 'new-url.com' --all-tables

# Dry run (test without changes)
wp search-replace 'old-url.com' 'new-url.com' --all-tables --dry-run

# Replace email addresses
wp search-replace '[email protected]' '[email protected]' --all-tables
```

**Multisite considerations**:
- Use `--url=` to target specific subsite
- Or use `--network` flag for network-wide operations
