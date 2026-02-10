## Git-Based Deployment Workflow

Seravo uses Git for code deployment. Every site has a Git remote named 'production'.

### Initial Setup
```bash
# Clone your site (if not already)
git clone ssh://$USER@$SITE.seravo.com:[$PORT]/data/wordpress ~/Projects/$SITE --origin production

# OR initialize in existing directory
cd /data/wordpress
git init
git remote add production ssh://$USER@$SITE.seravo.com:[$PORT]/data/wordpress
```

### Standard Workflow
```bash
# Make changes locally
vim wp-content/themes/mytheme/functions.php

# Commit changes
git add .
git commit -m "Add custom functionality"

# Git hooks run tests automatically (if configured)
# Tests run on commit

# Push to production
git push production master
# Output shows:
# - Object count
# - Tests run (if configured)
# - Deployment status
```

### Git Hooks

**wp-activate-git-hooks** sets up automated testing on every commit:
- Pre-commit: PHP lint, syntax checking
- Pre-push: Can run integration tests
- Configuration in `.git/hooks/`

**Best practices**:
1. Always commit with meaningful messages
2. Test locally in Docker/Vagrant before pushing
3. Review git hooks output for test failures
4. Keep commits atomic and focused
5. Use branches for experimental features

### SSH Configuration

Add to `~/.ssh/config` for easier access:
```
Host mysite
    HostName example.seravo.com
    Port 12345
    User example
    IdentityFile ~/.ssh/id_rsa
```

Then simply: `ssh mysite`
