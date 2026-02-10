## Security Best Practices

### SSH Key Management

**Always use SSH keys** (not passwords):
```bash
# Generate strong key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Or RSA 4096-bit
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Add to Seravo
ssh-copy-id -p 12345 user@example.seravo.com
```

**Multiple developers**:
- Each developer adds their own public key to `~/.ssh/authorized_keys`
- Don't share private keys
- Audit keys regularly: `cat ~/.ssh/authorized_keys`

### Credential Management

**Never hardcode credentials**:
- wp-config.php uses getenv() for all secrets
- View with: `wp-list-env`
- Override locally with .env (gitignored)

**Password reset workflow**:
```bash
# Check for breaches first
wp-check-haveibeenpwned <hash>

# Reset if compromised
wp-reset-all-passwords

# Or reset SSH password
wp-reset-ssh-password
```

### Access Auditing

**Monitor logins**:
```bash
# SSH access attempts
wp-last-ssh-logins

# WordPress logins
wp-last-wp-logins

# File changes
wp-backup-list-changes
```

**Suspicious activity response**:
```bash
# 1. Review changes
wp-backup-list-changes

# 2. Restore from backup if needed
# (Coordinate with Seravo support)

# 3. Reset all sessions
wp-reset-all-sessions

# 4. Change passwords
wp-reset-all-passwords

# 5. Audit users
wp user list --role=administrator
```

### Database Security

**No direct MySQL access from external networks**:
- Only via SSH tunnel
- Use wp db cli for shell access
- Get credentials: `wp-list-env | grep DB_`

**For external tools** (Sequel Pro, TablePlus):
```bash
# Set up SSH tunnel
ssh -L 3306:localhost:3306 -p 12345 user@example.seravo.com

# In another terminal, connect to localhost:3306
# Credentials from wp-list-env
```
