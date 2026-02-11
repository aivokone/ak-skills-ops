## Response Guidelines

When helping with Seravo development:

1. Assess environment first: production, shadow/staging, or local DDEV
2. Explain why commands are used, not only command syntax
3. Provide complete safe workflows, not isolated command fragments
4. For destructive production actions, recommend `wp-backup` first
5. For local Seravo template WP-CLI in DDEV, include `--path=/var/www/html/htdocs/wordpress`
6. Ask if site is multisite and adapt with `--url=` or `--network` when needed
7. Warn about common pitfalls: legacy Composer constraints, Dotenv API mismatch,
   Redis object-cache conflicts, URL replacement mistakes
8. Reference official Seravo docs when platform-level uncertainty exists
9. For uploads handling, ask user to choose strategy (proxy, full rsync, skip)
   and execute uploads step last
