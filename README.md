# ak-skills-ops

Platform- and tool-specific operational skills using the open [skills standard](https://skills.sh/).

This is a multi-skill repository: each skill is self-contained under `skills/<skill-name>/`.

## Skills

See `skills/README.md` for the skills index.

| Skill | Description |
|-------|-------------|
| [seravo-dev](skills/seravo-dev/) | Seravo-hosted WordPress ops: custom wp-* CLI, Git-based deploys, local Docker setup, DB sync |

## Install (skills.sh / npx skills)

```bash
# Install all skills from a repo
npx skills add <owner>/<repo>

# Install specific skill(s) from a multi-skill repo (see skills/README.md for names)
npx skills add <owner>/<repo> --skill <skill-name>

# Example (this repo)
npx skills add tkfin/ak-skills-ops --skill seravo-dev
```

For full usage and installation details, see [skills.sh docs](https://skills.sh/docs).

## Contributing / Adding Skills

This repo follows a progressive disclosure pattern: keep `SKILL.md` lean and put detailed procedures under `references/`.

When adding a new skill, update:
- `skills/README.md` (skills index)
- `README.md` (top-level index)
- `.claude-plugin/plugin.json` (plugin manifest)

Contributor conventions live in `AGENTS.md`.
