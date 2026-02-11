## Database Management Workflows

## Production -> Local (Recommended)

Use host-side SSH export + DDEV import instead of legacy local pull helpers.

```bash
ssh -4 -p PORT USER@HOST 'wp db export -' | gzip > /tmp/production.sql.gz
ddev import-db --file=/tmp/production.sql.gz
```

Run URL replacement in DDEV (path required for Seravo template layout):

```bash
ddev wp search-replace 'https://production-url' 'https://SLUG.ddev.site' \
  --all-tables --path=/var/www/html/htdocs/wordpress

ddev wp cache flush --path=/var/www/html/htdocs/wordpress
```

Verify:

```bash
ddev wp option get siteurl --path=/var/www/html/htdocs/wordpress
ddev wp option get home --path=/var/www/html/htdocs/wordpress
```

## Local -> Production (Dangerous)

Rarely needed; prefer code-only deploys. If required:

```bash
# From local
DDEV_DB_FILE=/tmp/local-db.sql
ddev export-db --file="$DDEV_DB_FILE"

# Copy to production
scp -P PORT "$DDEV_DB_FILE" USER@HOST:/data/db/local-db.sql

# On production host
ssh -4 -p PORT USER@HOST
wp-backup
cd /data/wordpress/htdocs
wp db reset --yes
wp db import /data/db/local-db.sql --skip-optimization
wp search-replace 'https://SLUG.ddev.site' 'https://production-url' --all-tables
wp-purge-cache
```

## Manual SQL Troubleshooting

If import fails with engine clauses from incompatible environments:

```bash
sed -i 's/ENGINE=InnoDB//g' database.sql
sed -i 's/ENGINE=MyISAM//g' database.sql
```

Then re-import with skip optimization when needed.

## Multisite Notes

- For site-specific rewrites, include `--url=`.
- For network-wide operations, use `--network` where applicable.
- Keep replacements explicit and reviewed before production operations.

## Legacy Note

`wp-pull-production-db` may still exist in older templates, but SSH export +
`ddev import-db` is the default recommendation.
