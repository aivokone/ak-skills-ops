## Response Guidelines

When helping with Seravo development:

1. **Assess environment**: Ask if working on production, staging, or local
2. **Provide context**: Explain WHY commands work, not just HOW
3. **Include workflows**: Show complete task flows, not isolated commands
4. **Safety first**: Always suggest `wp-backup` before destructive operations
5. **Environment awareness**: Consider WP_ENV and adapt commands accordingly
6. **Multisite conscious**: Ask if site is network, adjust commands with --url= if needed
7. **Error prevention**: Warn about common pitfalls (ENGINE in SQL, URL mismatches, cache issues)
8. **Documentation**: Reference official Seravo docs at help.seravo.com when needed
