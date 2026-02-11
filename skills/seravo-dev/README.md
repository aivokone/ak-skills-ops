# Seravo Developer Skill

A practical skill for WordPress development on Seravo's managed hosting platform,
with a DDEV-first local workflow.

## Sources, Attribution, and Scope

This skill is primarily based on Seravo's Help Center documentation:
- https://help.seravo.com/en/

It also includes opinionated additions and workflow changes by Aivokone:
- https://github.com/aivokone

These additions focus on practical safety checks and agent-friendly local workflows,
especially for Apple Silicon and non-interactive environments.

## What This Skill Covers

### Custom WP-CLI Commands on Seravo
- Core Seravo command set (`wp-backup`, `wp-purge-cache`, `wp-list-env`, and more)
- Safe usage guidance by environment
- Recovery-oriented command ordering

### Git Deployment Workflows
- Git-first deployment to Seravo production/shadow
- Remote and branch patterns
- Safe deployment practices with verification

### Local Development With DDEV
- DDEV bootstrap for Seravo template repositories
- SSH-based DB/content sync without Seravo local Docker container dependency
- Required WordPress CLI path usage in DDEV for Seravo directory layout

### Database Management
- Production -> local sync with SSH export stream + `ddev import-db`
- URL replacement and cache handling after import
- Dangerous local -> production flow with backup-first guardrails

### Troubleshooting and Performance
- SSH and access troubleshooting
- DDEV runtime and WP-CLI path issues
- Redis object cache conflicts in local setup
- Seravo-specific debugging commands and log paths

### Security Best Practices
- SSH key onboarding with IPv4-safe flow
- Session/password incident response
- Operational safety for production changes

## Key Differentiators

Compared with generic WordPress hosting workflows:
1. Seravo custom `wp-*` command ecosystem
2. Git-based deployment model
3. Bedrock-inspired directory layout under `/data/wordpress`
4. DDEV-first local workflow for better ARM and agent compatibility
5. SSH-first operations and hardened access practices

## Quick Start Prompts

### Backup before production changes
```text
I need to backup my Seravo site before making changes. What's the command?
```

### Deploy with Git
```text
I've made changes locally. Walk me through deploying to production using Git.
```

### Local setup with DDEV
```text
Set up this Seravo project locally with DDEV and explain required wp-config changes.
```

### Pull production data to local
```text
How do I import production database and plugins/themes to local DDEV safely?
```

### Troubleshoot caching
```text
Local WP-CLI fails with RedisException. What should I change in this Seravo template?
```

## Directory Structure

```text
seravo-dev/
├── SKILL.md
├── README.md
├── evals.json
└── references/
```

## Skill Philosophy

### Workflow + Context
Responses should explain both what to run and why, not only command snippets.

### Safety First
Potentially destructive operations require backup recommendation and a verification
step.

### Environment-Aware Guidance
Advice should adapt to `production`, `shadow/staging`, and `local` contexts.

### DDEV-First Local Strategy
Legacy Docker/Vagrant local commands are treated as deprecated guidance, not default
execution path.

## Usage Tips

For quick commands:
- "What's the command to backup?"
- "How do I clear cache on Seravo?"
- "How do I verify DB environment variables?"

For complete workflows:
- "Walk me through local setup + DB import + URL replacement."
- "How do I migrate a Seravo template to modern Composer/Dotenv support?"

For troubleshooting:
- "DDEV WP-CLI cannot find installation unless I pass --path"
- "Composer update fails due to old plugin API constraints"
- "Local Redis object-cache breaks CLI commands"

## Maintenance Notes

Track updates in:
- Seravo custom command set and host environment conventions
- DDEV and PHP version guidance
- Seravo template compatibility points (Composer constraints, Dotenv usage)
- Migration/troubleshooting patterns for ARM Apple Silicon environments

## Testing the Skill

Run evaluations using skill tooling:

```bash
# Test all scenarios
skill-creator eval seravo-dev-skill --all

# Test a specific workflow
skill-creator eval seravo-dev-skill --eval "Complete deployment workflow"
```

## Related Resources

- Seravo docs: https://help.seravo.com/en/
- Seravo developer docs: https://help.seravo.com/en/collections/929963-developer-documentation
- Seravo template repo: https://github.com/Seravo/wordpress
- DDEV docs: https://ddev.readthedocs.io/
- Aivokone profile: https://github.com/aivokone

## License

This skill follows Anthropic/Codex skill authoring conventions. Knowledge is
compiled from public documentation and operational experience.
