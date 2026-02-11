---
name: seravo-dev
description: "Seravo-hosted WordPress ops: custom wp-* CLI, Git-based deploys, local Docker/Vagrant setup, DB sync, and Seravo-specific troubleshooting."
---

# Seravo Developer Guide

Use this skill for Seravo-hosted WordPress work: deployment, environment setup, database sync, custom `wp-*` commands, and incident response/debugging.

## Scope

Use this skill when the user asks about:
- Seravo platform behavior or directory structure
- Seravo custom WP-CLI commands (for example `wp-backup`, `wp-pull-production-db`)
- Production/shadow/local workflow
- Git-based deployment on Seravo
- Docker/Vagrant local setup from Seravo templates
- Cache or performance issues specific to Seravo stack

Do not use this skill for generic WordPress questions that are not Seravo-specific.

## Fast Workflow

1. Identify environment first: `production`, `shadow/staging`, or `local`.
2. Confirm risk level before writes: read-only, local write, or production write.
3. Prefer safe diagnostics first (`status`, `logs`, dry-runs).
4. Before destructive operations, require backups and explicit confirmation.
5. When giving commands, provide exact runnable command sequence and verification step.

## Safety Rules

- Treat production DB import/export and URL replacement as high risk.
- Always run or require backup before production-changing tasks.
- Avoid irreversible commands unless user explicitly requests them.
- After state changes, include verification and cache purge steps.

## Command Routing

- Backup/restore tasks: use `wp-backup*` commands.
- Pull production data to local: use `wp-pull-production-db`.
- Sync code/content from production: use `wp-pull-production-plugins`, `wp-pull-production-themes`, `wp-pull-production-core`.
- Cache invalidation: use `wp-purge-cache`.
- Local environment bootstrap: prefer `docker-compose up -d` + `docker exec -u vagrant ... wp-pull-*` for imports; `wp-development-up` is optional and may loop in some setups.

## Answer Style

- Start with environment assumptions.
- Provide minimal safe command path first.
- Include rollback or recovery command when applicable.
- Keep output focused on actionable steps, not platform history.

## Reference Loading (Progressive Disclosure)

Detailed procedures are intentionally moved out of this file.

- Guide index (small): `references/seravo-guide.md`
- Guide sections (open only one): `references/seravo-guide/*.md`
- Seravo -> local -> GitHub private import recipe: `references/seravo-to-local-to-github.md`

Load only the relevant section file based on task.
Use targeted search across the section directory instead of opening everything:

```bash
rg -n "wp-pull-production-db|wp-backup|wp-purge-cache|config\\.yml|deployment|troubleshooting|performance|multisite|ssh|keyscan|copy-id|AddressFamily" references/seravo-guide
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

Multisite specifics are sprinkled across multiple sections; search for `Multisite` / `--url=` and open only the file you need.

Use `references/seravo-to-local-to-github.md` when the user asks for the end-to-end workflow:
- Seravo production clone -> local Docker -> `wp-pull-*` -> push to GitHub private
- Container name conflicts (`SITE`), Composer allow-plugins, running `wp-pull-*` as `vagrant`
- Staging/shadow port + host key verification
- GitHub push issues caused by `core.sshCommand` and how to avoid them (HTTPS origin + `gh auth setup-git`)
