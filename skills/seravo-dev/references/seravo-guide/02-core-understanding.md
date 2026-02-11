## Core Understanding

Seravo is a Finnish managed WordPress hosting provider with a developer-first
philosophy. Key characteristics:

### Platform Philosophy
- SSH-first operations (no FTP workflow)
- Git-based deployment
- Bedrock-inspired directory structure under `/data`
- Security-focused credential handling via environment variables
- Custom `wp-*` command suite for operational tasks

### Environment Types
1. Production: Live site at `example.seravo.com` or custom domain
2. Shadow (staging): Testing environment with separate SSH port
3. Development: Local DDEV project mirroring Seravo template layout

### Directory Structure (Seravo)

```text
/data/
├── wordpress/                    # Git repository root
│   ├── htdocs/                  # Web root
│   │   ├── wp-content/          # Site code/content
│   │   ├── wordpress/           # WordPress core (separate)
│   │   └── wp-config.php        # Uses environment variables
│   └── .git/                    # Version control
├── backups/                     # Automated backups
├── db/                          # Database exports
└── home/$USER/                  # User home directory
```

Critical path note: WordPress core lives in
`/data/wordpress/htdocs/wordpress/` while site code is in
`/data/wordpress/htdocs/wp-content/`.

### Local Development Principle

Use DDEV as the default local runtime. Prefer direct SSH + rsync workflows for
pulling production data/content instead of legacy Docker/Vagrant helper commands.
