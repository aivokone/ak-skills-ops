# Seravo Developer Skill

A comprehensive skill for WordPress development on Seravo's managed hosting platform, created specifically for developer workflows.

## Sources, Attribution, and Scope

This skill is primarily based on Seravo's Help Center documentation:

- https://help.seravo.com/en/

It also includes **opinionated additions and changes by Aivokone**:

- https://github.com/aivokone

These additions cover practical conventions, extra safety checks, and workflow preferences that may go beyond (or differ from) the official Seravo docs. This skill is not an official Seravo publication.

## What This Skill Does

This skill provides expert guidance on:

### 🛠️ Custom WP-CLI Commands
- Complete reference for all Seravo-specific `wp-*` commands
- Context about when and why to use each command
- Examples for backup, database sync, cache management, security, and more

### 🚀 Git Deployment Workflows  
- Git-based deployment to production
- Git hooks and automated testing
- Safe deployment practices

### 🐳 Local Development Setup
- Docker and Vagrant (legacy) configuration
- `config.yml` setup and management
- Asset proxy for media files
- Environment parity with production

### 🗄️ Database Management
- Syncing databases between environments (production ↔ local ↔ shadow)
- URL search-replace workflows
- Multisite domain mapping
- Manual export/import procedures

### 🔧 Troubleshooting & Performance
- Common issues and solutions
- Performance measurement tools
- Cache debugging
- Security auditing
- Access monitoring

### 🏢 WordPress Multisite
- Network-specific commands
- Subdomain/subfolder configuration
- Database table structure understanding

### 🔐 Security Best Practices
- SSH key management
- Seravo IPv4-safe SSH bootstrap (`AddressFamily inet`, `ssh-keyscan -4`, `sshpass` fallback for non-interactive shells)
- Password and session resets
- Credential handling
- Access auditing

### 📦 Advanced Topics
- Custom Nginx configuration
- Composer dependency management
- Environment-specific code
- Shadow (staging) environment usage

## Key Differentiators

Unlike generic WordPress hosting, Seravo provides:

1. **Custom tooling**: 30+ custom `wp-*` commands for common tasks
2. **Git-first deployment**: Push code directly to production via Git
3. **Bedrock-inspired structure**: Modified directory layout with `/data` namespace
4. **Environment parity**: Docker containers that exactly mirror production
5. **SSH-only access**: No FTP, enhanced security model
6. **Finnish hosting**: Based in Finland with Finnish support

## Quick Start Examples

### Get help with backups
```
I need to backup my Seravo site before making changes. What's the command?
```

### Git deployment
```
I've made changes locally. Walk me through deploying to production using Git.
```

### Database sync
```
How do I pull the production database to my local environment?
```

### Troubleshooting
```
Changes aren't showing on my site. How do I clear all caches?
```

### Local setup
```
I'm setting up local development with Docker. What's the process?
```

## Evaluation Coverage

The skill includes 25 comprehensive evaluation cases covering:

- Command reference and usage
- Complete workflows (development → deployment)
- Configuration file setup
- Database operations
- Performance debugging
- Security workflows
- Migration procedures
- Multisite management
- Troubleshooting scenarios

## Directory Structure

```
seravo-dev/
├── SKILL.md              # Main skill file (comprehensive guide)
├── README.md             # This file
├── evals.json            # Per-skill evaluation test cases
└── references/           # Detailed procedures and topic-specific docs
```

## Installation

To use this skill:

1. **Via claude.ai Projects**: Upload `SKILL.md` as project knowledge
2. **Via API**: Include skill content in system prompt or use as tool documentation
3. **For testing**: Use skill-creator to run evaluations

## Skill Philosophy

### Comprehensive Context
This skill provides **workflows with context**, not just command lists. Each response explains:
- What the command does
- When to use it
- What to watch out for
- Related commands and workflows

### Developer-Focused
Written for developers who:
- Understand terminal/SSH usage
- Work with Git version control  
- Need production-ready workflows
- Value understanding over copy-paste

### Safety-First
Every destructive operation includes:
- Backup recommendations
- Warnings about risks
- Verification steps
- Recovery procedures

### Environment-Aware
Guidance adapts to:
- Production vs staging vs local
- Single site vs WordPress Network
- Different WP_ENV contexts
- Security implications per environment

## Usage Tips

### For Quick Commands
Ask directly:
- "What's the command to backup?"
- "How do I clear cache?"
- "Show me database export syntax"

### For Workflows
Ask for complete flows:
- "Walk me through deploying changes"
- "How do I set up local development?"
- "What's the migration workflow?"

### For Troubleshooting
Describe the issue:
- "Database import is failing with ENGINE errors"
- "Can't SSH to my site"
- "Site is slow, what debugging tools exist?"

### For Configuration
Ask about setup:
- "What goes in config.yml?"
- "How do I configure multisite locally?"
- "Where do custom Nginx configs go?"

## What Makes This Skill Special

1. **Built from official documentation**: Primarily compiled from https://help.seravo.com/en/
2. **Real-world workflows**: Based on actual developer patterns and needs
3. **Complete coverage**: All four priority areas with comprehensive detail
4. **Finnish context**: Understands Seravo's unique position in Finnish WordPress hosting
5. **Opinionated improvements**: Additions and changes by Aivokone to make guidance more actionable for day-to-day developer ops
6. **Production-tested patterns**: Workflows that work in real Seravo environments

## Skill Maintenance

This skill is based on Seravo's documentation as of February 2025. Key areas to watch for updates:

- New `wp-*` commands added by Seravo
- Docker image changes (Vagrant is already deprecated)
- config.yml schema updates
- New Nginx/PHP versions
- Additional environment variables

## Testing the Skill

Run evaluations using skill-creator:

```bash
# Test all scenarios
skill-creator eval seravo-dev-skill --all

# Test specific workflow
skill-creator eval seravo-dev-skill --eval "Git deployment workflow"

# Benchmark performance
skill-creator benchmark seravo-dev-skill
```

## Contributing

To improve this skill:

1. Add new evaluation cases for uncovered scenarios
2. Update command references when Seravo releases new tools
3. Add troubleshooting patterns for common issues
4. Expand multisite and advanced configuration examples

## Related Resources

- **Seravo Documentation (primary source)**: https://help.seravo.com/en/
- **Developer Docs**: https://help.seravo.com/en/collections/929963-developer-documentation
- **GitHub Template**: https://github.com/Seravo/wordpress
- **Aivokone (opinionated additions)**: https://github.com/aivokone
- **Docker Hub**: Search for Seravo's WordPress images

## License

This skill is created for use with Claude and follows Anthropic's skill guidelines. The knowledge is compiled from publicly available Seravo documentation and includes opinionated additions and changes by Aivokone.

---

**Created with**: skill-creator  
**Target audience**: WordPress developers using Seravo hosting  
**Skill level**: Comprehensive guide with workflows + context  
**Coverage**: All major developer workflows and troubleshooting scenarios
