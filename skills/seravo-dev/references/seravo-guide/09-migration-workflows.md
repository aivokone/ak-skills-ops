## Migration Workflows

### Migrating TO Seravo

**Method 1: SSH + WP-CLI** (Recommended)

On old server:
```bash
# Export database
wp db export ~/wordpress-database.sql

# Verify export
ls -lh ~/wordpress-database.sql
```

On new Seravo server:
```bash
# Copy database
rsync -avz --stats olduser@oldserver:~/wordpress-database.sql /data/db/

# Copy wp-content
rsync -avz --stats olduser@oldserver:/var/www/htdocs/wp-content /data/wordpress/htdocs/

# Import database
cd /data/wordpress/htdocs
wp db reset --yes
wp db import /data/db/wordpress-database.sql --skip-optimization

# Fix URLs
wp search-replace 'http://oldsite.com' 'https://newsite.seravo.com' --all-tables

# Clear caches
wp-purge-cache

# Test
wp option get siteurl
wp option get home
```

**Method 2: Backup plugin** (Easier but slower)

1. Install backup plugin on old site
2. Create full backup (files + database)
3. Download backup archive
4. Upload to Seravo via SFTP
5. Use plugin to restore

**Post-migration checklist**:
- [ ] Verify all pages load
- [ ] Test admin login
- [ ] Check media files
- [ ] Verify forms/contact
- [ ] Test search functionality
- [ ] Check multisite subsites (if applicable)
- [ ] Update DNS to point to Seravo
- [ ] Force HTTPS: `wp search-replace 'http://' 'https://' --all-tables`
- [ ] Clear all caches: `wp-purge-cache`

### Migrating FROM Seravo

**Export everything**:
```bash
# SSH to Seravo
ssh user@example.seravo.com -p 12345

# Backup first
wp-backup

# Export database
wp db export /data/db/export.sql

# Create archive
cd /data
tar -czf site-export.tar.gz wordpress/htdocs/wp-content db/export.sql

# Download
# From local machine:
scp -P 12345 user@example.seravo.com:/data/site-export.tar.gz ./
```
