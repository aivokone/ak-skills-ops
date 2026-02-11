# Seravo Developer Guide (Index)

This guide is split into small files under `references/seravo-guide/` so an agent can open only the relevant section instead of loading a large monolith.

## Sections

- `references/seravo-guide/01-seravo-local-github-private-import.md`
- `references/seravo-guide/02-core-understanding.md`
- `references/seravo-guide/03-custom-wp-cli-commands.md`
- `references/seravo-guide/04-git-based-deployment-workflow.md`
- `references/seravo-guide/05-local-development-setup.md`
- `references/seravo-guide/06-database-management-workflows.md`
- `references/seravo-guide/07-troubleshooting-performance.md`
- `references/seravo-guide/08-security-best-practices.md`
- `references/seravo-guide/09-migration-workflows.md`
- `references/seravo-guide/10-quick-reference.md`
- `references/seravo-guide/11-response-guidelines.md`
- `references/seravo-guide/12-advanced-topics.md`
- `references/seravo-guide/13-getting-help.md`


## How To Search (Without Opening Everything)

- List sections: `ls references/seravo-guide`
- Search across sections: `rg -n "wp-backup|wp-pull-production-db|wp-purge-cache|config.yml|deployment|multisite|ssh|keyscan|copy-id|AddressFamily" references/seravo-guide`
- After you find a hit, open only a small slice around it:
  - Example: `rg -n "wp-pull-production-db" references/seravo-guide/03-custom-wp-cli-commands.md`
  - Then: `sed -n '120,220p' references/seravo-guide/03-custom-wp-cli-commands.md`

If you need the end-to-end Seravo -> local -> GitHub workflow, use `references/seravo-to-local-to-github.md`.
