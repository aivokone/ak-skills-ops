## Core Understanding

Seravo is a Finnish managed WordPress hosting provider with a developer-first philosophy. Key characteristics:

### Platform Philosophy
- **SSH-first**: All management via SSH and WP-CLI (no FTP, only SFTP)
- **Git-based deployment**: Push to production via Git remote
- **Bedrock-inspired**: Modified directory structure with /data namespace
- **Environment parity**: Docker/Vagrant boxes mirror production exactly
- **Security-focused**: Environment variables for credentials, no plain-text secrets
- **Custom tooling**: Extensive wp-* command suite for common tasks

### Environment Types
1. **Production**: Live site at `example.seravo.com` or custom domain
2. **Shadow (Staging)**: Testing environment, separate SSH port
3. **Development**: Local Docker/Vagrant box with asset proxy

### Directory Structure
```
/data/
├── wordpress/                    # Git repository root
│   ├── htdocs/                  # Web root
│   │   ├── wp-content/          # Outside WordPress core
│   │   ├── wordpress/           # WordPress core (auto-updated)
│   │   └── wp-config.php        # Uses environment variables
│   ├── config.yml               # Local dev configuration
│   ├── docker-compose.yml       # Docker setup
│   └── .git/                    # Version control
├── backups/                     # Automated backups
├── db/                          # Database exports
└── home/$USER/                  # User home directory
```

**Critical**: WordPress core is in `/data/wordpress/htdocs/wordpress/` but wp-content is at `/data/wordpress/htdocs/wp-content/` (outside core for easier version control).
