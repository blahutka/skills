# Personal project skills

Reusable Rails development guidance for coding agents. Each folder under `skills/` is an independent Agent Skill with a `SKILL.md` entry point. Codex UI metadata is optional and stored in `agents/openai.yaml`.

| Skill | Use it for |
| --- | --- |
| [facilitating-ddd-discovery](skills/facilitating-ddd-discovery/SKILL.md) | Stakeholder discovery, domain vocabulary, boundary rules, and project-aware APIs |
| [rails-packs](skills/rails-packs/SKILL.md) | Domain packs, Packwerk boundaries, namespaces, and public interfaces |
| [rails-servus](skills/rails-servus/SKILL.md) | Servus actions, controller responses, and events between domains |
| [rails-rubyllm](skills/rails-rubyllm/SKILL.md) | Optional domain-owned AI agents and background execution |

## Install in Codex

Open your application project in Codex and send:

> Use the skill installer to install rails-packs and rails-servus from https://github.com/blahutka/skills into this project's .agents/skills/. Copy the complete selected folders, including references and agents metadata. Record the source commit. Do not install globally, overwrite existing skills, or change application dependencies.

For AI features, include `rails-rubyllm` in the request. Skills should be available on the next turn; if they do not appear, reopen the project/session.

Invoke a skill explicitly:

```text
$rails-packs Add a billing pack following this project's conventions.
$rails-servus Connect the domains through Servus events.
$rails-rubyllm Add an optional invoice review agent to billing.
```

Codex discovers project skills in `.agents/skills/`. See [official Codex skill documentation](https://developers.openai.com/codex/skills/).

## Install in Claude Code

Start Claude Code in your application project and send:

> Install rails-packs and rails-servus from https://github.com/blahutka/skills into this project's .claude/skills/. Copy the complete selected folders, including references. Record the source commit. Do not install globally, overwrite existing skills, or change application dependencies.

For AI features, include `rails-rubyllm`. Invoke a skill explicitly:

```text
/rails-packs Add a billing pack following this project's conventions.
/rails-servus Connect the domains through Servus events.
/rails-rubyllm Add an optional invoice review agent to billing.
```

Claude Code discovers project skills in `.claude/skills/`. If a new skill is not listed, restart the session from the project root. The optional `agents/openai.yaml` file provides Codex metadata; Claude Code does not need it. See [official Claude Code skill documentation](https://code.claude.com/docs/en/skills).

This repository provides standalone skills, not a Claude plugin marketplace. Use folder installation rather than `/plugin marketplace add` or `/plugin install`.

## Manual installation (macOS/Linux)

From your application project root, choose the destination:

```sh
# Codex:
SKILLS_DEST=".agents/skills"
# Claude Code: use this instead:
# SKILLS_DEST=".claude/skills"
```

Then copy the selected skills using this Bash snippet. It refuses to replace an existing skill. Requires Git and Python 3.

```bash
(
  set -eu
  skills_source=$(mktemp -d)
  trap 'rm -rf "$skills_source"' EXIT
  git clone https://github.com/blahutka/skills.git "$skills_source/repository"

  # Optional: select an existing tag or commit before copying.
  # git -C "$skills_source/repository" checkout <tag-or-commit>

  python3 - "$skills_source/repository" "$SKILLS_DEST" <<'INSTALL'
import shutil
import subprocess
import sys
from pathlib import Path

source, destination = map(Path, sys.argv[1:])
selected = ["rails-packs", "rails-servus"]
# Include "rails-rubyllm" above if the project needs AI support.
for name in selected:
    target = destination / name
    if target.exists() or target.is_symlink():
        raise SystemExit(f"Skill already exists; review before updating: {target}")
for name in selected:
    shutil.copytree(source / "skills" / name, destination / name)
commit = subprocess.check_output(
    ["git", "-C", str(source), "rev-parse", "HEAD"], text=True
).strip()
print(f"Installed {', '.join(selected)} into {destination}")
print(f"Source: https://github.com/blahutka/skills at {commit}")
INSTALL
)
```

Record the printed source commit in your project documentation. Run separately with each destination if the team uses both tools. Commit the installed skill directories so collaborators receive the same versions.

## Use and update

| Tool | Project-local directory | Explicit invocation |
| --- | --- | --- |
| Codex | `.agents/skills/<name>/` | `$rails-packs` |
| Claude Code | `.claude/skills/<name>/` | `/rails-packs` |

Both tools can also select relevant skills automatically. Install complete folders, including `references/`; copying only SKILL.md omits the examples. A direct [skill folder URL](https://github.com/blahutka/skills/tree/main/skills/rails-rubyllm) can identify an individual skill.

Installation copies coding guidance. It does not install gems, configure providers, or create application agents. Keep project-specific versions, enabled features, commands, and mandatory architecture in AGENTS.md for Codex and CLAUDE.md for Claude Code. When using both, keep shared policies consistent; do not assume both tools automatically read the same instruction file.

For reproducible installations, select a commit SHA or an existing release tag instead of moving `main`. Review diffs before updating existing copies; they do not update automatically. No release tag is required to install from a commit.

## Add another skill

Create `skills/<name>/SKILL.md` with YAML frontmatter containing `name` and a precise `description`. Keep the entry point focused; put substantial conditional examples in linked `references/` files. Add scripts or assets only when they support a real workflow. Update the catalog above and validate frontmatter, local links, and any executable examples.

The folder format is based on the [Agent Skills standard](https://agentskills.io/specification). Repository-local discovery is documented in [Codex skills](https://developers.openai.com/codex/skills/). No marketplace registration is required for copying individual skill folders; this repository does not currently provide a Claude marketplace manifest.
