# Seravo -> Local (Docker) -> GitHub Private: Import Recipe

This is a practical, network-aware import workflow that avoids common Seravo template gotchas.

## Preconditions

- You have Seravo SSH access to the target environment(s).
- You have Docker + Compose locally.
- You have GitHub auth available (`gh auth status` works) if pushing to GitHub.

Note: In some agent/sandbox environments, `git clone` and GitHub API calls may require an escalated run due to DNS/network restrictions.

## 1) Clone From Seravo (Network-Aware)

Clone the production repo into an empty directory:

```bash
git clone --origin production ssh://USER@HOST:PORT/data/wordpress .
```

- First connection may block on host key verification. Prefer explicit host key registration over IPv4:

```bash
ssh-keyscan -4 -p PORT HOST >> ~/.ssh/known_hosts
ssh -4 -p PORT USER@HOST 'echo OK'
```

- For repeated usage, add `AddressFamily inet` for Seravo hosts in `~/.ssh/config` to avoid IPv6-first connection failures.

## 2) Prevent Docker Container Name Conflicts Immediately

Seravo templates often use `container_name: ${SITE:-wordpress}`. If you already have a container named `wordpress`, Compose will fail.

Create `.env` before starting Compose:

```bash
cat > .env <<'ENV'
SITE=project-slug
ENV
```

Pick a unique slug per project (for example `akha`).

## 3) Start Docker

```bash
docker-compose up -d
# verify
docker ps
```

Determine the container name:

```bash
docker ps --format '{{.Names}}' | rg -n "${SITE}" || docker ps --format '{{.Names}}'
```

In Seravo templates, the main container is typically `$SITE` or includes it.

## 4) Ensure WordPress Core Exists Before `wp-pull-production-db`

If `htdocs/wordpress` is missing, Seravo `wp-pull-*` commands can fail because there is no WP install to operate on.

Check:

```bash
test -d htdocs/wordpress && echo "WP core exists" || echo "WP core missing"
```

If missing, fix Composer allow-plugins (common on newer Composer defaults) and install:

```bash
composer config --no-plugins allow-plugins.johnpbloch/wordpress-core-installer true
composer config --no-plugins allow-plugins.koodimonni/composer-dropin-installer true
composer config --no-plugins allow-plugins.composer/installers true
composer install --no-interaction
```

Rationale: without these allow-plugins entries, WordPress core installers may be blocked and the WP directory never materializes.

## 5) Run `wp-pull-*` Commands As `vagrant` (Never root)

Some commands refuse to run as root (`Cannot run as root`). Prefer explicit user selection.

Run inside the container as `vagrant`:

```bash
CONTAINER=your-container-name

docker exec -it -u vagrant "$CONTAINER" bash -lc 'wp-pull-production-db'
```

Other pulls:

```bash
docker exec -it -u vagrant "$CONTAINER" bash -lc 'wp-pull-production-plugins'
docker exec -it -u vagrant "$CONTAINER" bash -lc 'wp-pull-production-themes'
docker exec -it -u vagrant "$CONTAINER" bash -lc 'wp-pull-production-core'
```

Interactive prompts to anticipate:

- `wp-pull-production-db` may ask URL mapping confirmation: answer `y` only if the mapping matches your local domain.
- `wp-pull-production-plugins` may ask: "update composer.json … (y/n)". For a clone/import flow, default to `n` unless you intentionally want it to modify Composer config.

Verification:

```bash
docker exec -u vagrant "$CONTAINER" bash -lc 'wp option get siteurl && wp option get home'
```

Note: `wp-purge-cache` is meaningful on Seravo servers, but on local development it may be a no-op. Mention that expectation explicitly when recommending it.

## 6) Prefer Direct `docker exec ... wp-pull-*` Over `wp-development-up` For Imports

If `wp-development-up` loops on "Waiting for … start", skip it and use the explicit `docker-compose up -d` + `docker exec -u vagrant …` approach above. It is more debuggable and avoids hidden wait loops.

## 7) SSH Keys: Project-Key Pattern (Optional but Recommended)

If you need a dedicated key for this project:

- Create `./.ssh/` and ensure it is ignored.
- Generate an ed25519 key:

```bash
mkdir -p .ssh
ssh-keygen -t ed25519 -f .ssh/seravo -N '' -C 'seravo-project'
```

- Add the public key to the Seravo environment `authorized_keys` (server write, but generally safe).
- For container usage, copy the key for the `vagrant` user and add an SSH config with host+port.

If password bootstrap is needed in a non-interactive shell:
```bash
if ! command -v sshpass >/dev/null; then
  echo "Install sshpass first (for example: brew install hudochenkov/sshpass/sshpass)"
fi

SSHPASS='YOUR_PASSWORD' sshpass -e ssh-copy-id -o AddressFamily=inet -i .ssh/seravo.pub -p PORT USER@HOST
```

Non-interactive check:

```bash
ssh -4 -o BatchMode=yes -i .ssh/seravo -p PORT USER@HOST 'echo OK'
```

## 8) Shadow/Staging Support

- Staging may be the same host with a different SSH port.
- You may need to add the same public key to staging `authorized_keys` separately.

Git remote example:

```bash
git remote add staging ssh://USER@HOST:STAGING_PORT/data/wordpress
# verify without deploying
GIT_SSH_COMMAND="ssh -o StrictHostKeyChecking=accept-new" git push --dry-run staging main
```

## 9) GitHub Private Repo: Avoid SSH Key Collisions

Gotcha: if the repo has `git config core.sshCommand ...` set for Seravo, GitHub SSH pushes may incorrectly use the Seravo key.

Recommendation:

- Keep Seravo remotes as SSH.
- Use GitHub `origin` over HTTPS and configure git credential helper via `gh`.

```bash
# ensures git is configured to use GitHub credentials
gh auth setup-git

# add GitHub HTTPS origin
git remote add origin https://github.com/OWNER/REPO.git

git branch -M main

git add -A
git commit -m "Initial import from Seravo (DOMAIN)"

git push -u origin main
```

If you must use SSH for GitHub, do not set `core.sshCommand` globally for the repo. Prefer per-command overrides:

```bash
GIT_SSH_COMMAND='ssh -i .ssh/seravo' git fetch production
```

## 10) `.gitignore` Strategy: Code-Only vs Full Clone

Seravo templates may ignore `composer.lock` or `wp-content/plugins/*` by default. That may be wrong for "full clone" to GitHub.

Choose one:

- Code-only repo: plugins/themes managed by Composer and lockfiles committed.
- Full clone repo: track `wp-content` code in git; still ignore mutable runtime data.

Minimum changes for "full clone":

- Commit lockfiles.
- Do not ignore:
  - `htdocs/wp-content/plugins/`
  - `htdocs/wp-content/mu-plugins/`
- Keep ignored:
  - `uploads/`, caches, backups
