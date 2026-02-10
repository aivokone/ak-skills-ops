## Local Development Setup

Seravo provides Docker and Vagrant options. **Docker is recommended** (Vagrant is deprecated).

### Docker Setup

**Prerequisites**:
- Docker installed
- Git repository initialized
- config.yml configured

**Starting Container**:
```bash
cd /path/to/project
docker-compose up
# OR detached mode:
docker-compose up -d

# Container will print:
# Success!
# Visit your site at https://wordpress.local/
# To enter: ssh wordpress.local -F .vagrant/ssh/config
```

**Accessing Container**:
```bash
# SSH into container
ssh wordpress.local -F .vagrant/ssh/config

# OR exec as root (for troubleshooting)
docker-compose exec wordpress bash

# Inside container, run commands:
wp-development-up  # Import DB/plugins/themes
wp plugin list
wp-purge-cache
```

**File Synchronization**:
- Volumes defined in docker-compose.yml
- Local directory mounted to /data/wordpress
- Changes reflect immediately

### config.yml Configuration

Located at `/data/wordpress/config.yml`. Controls local development behavior.

**Basic Structure**:
```yaml
name: mysite

production:
  domain: example.seravo.com
  ssh_port: 12345

staging:
  domain: example.seravo.com
  ssh_port: 23456

development:
  domains:
    - mysite.local
    - subdomain.mysite.local
  pull_production_db: never
  pull_production_plugins: never
  pull_production_themes: never
  avahi: true  # For .local domain resolution
```

**Key Configuration Options**:

`name`: Site identifier (used in container naming)

`production.domain`: Production SSH host

`production.ssh_port`: Production SSH port (from credentials email)

`development.domains`: Local domain names (for Nginx configuration)
- First domain is primary
- Add all multisite subdomains here

`pull_production_db`: Auto-sync behavior
- `always`: Pulls DB on vagrant up
- `never`: Manual control
- Recommendation: Use `never` and call `wp-pull-production-db` manually

`pull_production_plugins` / `pull_production_themes`: Same options as DB

`avahi`: Enable .local domain resolution (true/false)

**Multisite Configuration**:
```yaml
development:
  domains:
    - wordpress.local
    - site1.wordpress.local
    - site2.wordpress.local
```

Subdomains auto-map during `wp-pull-production-db`:
- site1.example.com → site1.wordpress.local
- site2.example.com → site2.wordpress.local

### Environment Variables (.env)

**Local override with .env**:
```bash
# Create environment-specific file
cat > .env.development << EOF
WP_TEST_URL=https://example.dev
DOMAIN_CURRENT_SITE=example.dev
NOBLOGREDIRECT=https://example.dev
COOKIE_DOMAIN=.example.dev
EOF

# Symlink to activate
ln -s .env.development .env
```

**Dotenv versions**:
- 5.0.0+: `Dotenv::createUnsafeImmutable($root_dir)`
- 2.4.0: `new Dotenv\Dotenv($root_dir)` with `->overload()`
- "Unsafe" needed for getenv() to work in PHP

**Security note**: .env is gitignored by default. Never commit it.

### Asset Proxy

Local development can automatically fetch missing media files from production:
- Configured via `wp-activate-asset-proxy`
- Requires working internet and production domain in config.yml
- Fetches uploads/ files on-the-fly when not present locally
- Saves bandwidth and disk space during development
