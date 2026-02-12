---
name: seravo-dev
description: "Seravo-hosted WordPress ops: custom wp-* CLI, Git-based deploys, DDEV local setup, DB sync, and Seravo-specific troubleshooting."
---

# Seravo Developer Guide

Use this skill for Seravo-hosted WordPress work: deployment, environment setup,
database sync, custom `wp-*` commands, and incident response/debugging.

## Scope

Use this skill when the user asks about:
- Seravo platform behavior or directory structure
- Seravo custom WP-CLI commands (for example `wp-backup`, `wp-purge-cache`)
- Production/shadow/local workflow
- Git-based deployment on Seravo
- DDEV local setup for Seravo template projects
- Database/content sync between Seravo and local
- Cache, Redis, SSH, or performance issues specific to Seravo stack

Do not use this skill for generic WordPress questions that are not Seravo-specific.

## Upstream Documentation

- Primary docs: https://help.seravo.com/en/
- Seravo developer docs: https://help.seravo.com/en/collections/929963-developer-documentation
- Seravo template repo: https://github.com/Seravo/wordpress

This skill intentionally adds DDEV-first local workflows and stricter safety
guardrails for agent execution.

## Fast Workflow

1. Identify environment first: `production`, `shadow/staging`, or `local`.
2. Confirm risk level before writes: read-only, local write, or production write.
3. Prefer safe diagnostics first (`status`, `logs`, dry-runs).
4. Before destructive operations, require backups and explicit confirmation.
5. For local setup and sync, use DDEV-first commands and explicit verification.

## Safety Rules

- Treat production DB import/export and URL replacement as high risk.
- Always run or require backup before production-changing tasks.
- Avoid irreversible commands unless user explicitly requests them.
- After state changes, include verification and cache purge/flush steps.

## Command Routing

- Backup/restore tasks: use `wp-backup*` commands.
- Local environment bootstrap: use `ddev config` and `ddev start`.
- Pull production DB to local: use SSH export stream + `ddev import-db`.
- Sync local code/content from production: use direct `rsync` over SSH.
- Local WordPress CLI in Seravo template layout: use `ddev wp ... --path=/var/www/html/htdocs/wordpress`.
- Seravo cache invalidation on servers: use `wp-purge-cache`.
- Local cache invalidation in DDEV: use `ddev wp cache flush --path=/var/www/html/htdocs/wordpress`.

Legacy local commands (`wp-development-up`, `wp-pull-production-*`, Docker/Vagrant
container exec) are deprecated and should not be recommended as primary workflow.

## Answer Style

- Start with environment assumptions.
- Provide minimal safe command path first.
- Include rollback or recovery command when applicable.
- Keep output focused on actionable steps, not platform history.
- When local setup includes uploads, ask user to choose strategy (proxy, full rsync,
  or skip) before running uploads step.

## Reference Loading (Progressive Disclosure)

Detailed procedures are intentionally moved out of this file.

- Guide index (small): `references/seravo-guide.md`
- Guide sections (open only one): `references/seravo-guide/*.md`
- Seravo -> local -> GitHub private import recipe: `references/seravo-to-local-to-github.md`

Load only the relevant section file based on task.
Use targeted search across the section directory instead of opening everything:

```bash
rg -n "ddev|wp-backup|wp-purge-cache|search-replace|--path=/var/www/html/htdocs/wordpress|composer update|createMutable|object-cache|deployment|troubleshooting|multisite|ssh|keyscan|copy-id|AddressFamily" references/seravo-guide
```

Then open only the matching section file and the relevant subsection(s) inside it.

## Task-to-Section Map

Use these section files under `references/seravo-guide/`:
- Platform basics: `references/seravo-guide/02-core-understanding.md`
- Seravo CLI catalog: `references/seravo-guide/03-custom-wp-cli-commands.md`
- Deployment flow: `references/seravo-guide/04-git-based-deployment-workflow.md`
- Local setup: `references/seravo-guide/05-local-development-setup.md`
- Database operations: `references/seravo-guide/06-database-management-workflows.md`
- Incidents/perf debugging: `references/seravo-guide/07-troubleshooting-performance.md`
- Hardening: `references/seravo-guide/08-security-best-practices.md`
- Migrations: `references/seravo-guide/09-migration-workflows.md`
- Quick command lookup: `references/seravo-guide/10-quick-reference.md`

Multisite specifics are sprinkled across multiple sections; search for `Multisite`
and `--path=/var/www/html/htdocs/wordpress`, then open only the file you need.

Use `references/seravo-to-local-to-github.md` when the user asks for the end-to-end workflow:
- Seravo production clone -> local DDEV -> DB import via SSH stream -> push to GitHub private
- Composer 1 -> 2 dependency migration and Dotenv v5 compatibility update
- Local Redis object cache disable and required WP-CLI `--path` usage in DDEV
- Staging/shadow port + host key verification
- GitHub push issues caused by `core.sshCommand` and how to avoid them (HTTPS origin + `gh auth setup-git`)
