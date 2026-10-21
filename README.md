# Personal project skills

Reusable Rails development guidance for coding agents. Each folder under `skills/` is an independent Agent Skill with a `SKILL.md` entry point. Codex UI metadata is optional and stored in `agents/openai.yaml`.

| Skill | Use it for |
| --- | --- |
| [rails-packs](skills/rails-packs/SKILL.md) | Domain packs, Packwerk boundaries, namespaces, and public interfaces |
| [rails-servus](skills/rails-servus/SKILL.md) | Servus actions, controller responses, and events between domains |
| [rails-rubyllm](skills/rails-rubyllm/SKILL.md) | Optional domain-owned AI agents and background execution |

## Install into one project

Tell your coding agent:

> Install the rails-packs and rails-servus skills from https://github.com/blahutka/skills into this project's .agents/skills/. Copy the complete selected skill folders, including references and optional agents metadata. Record the source commit. Do not install them globally or change application dependencies.

For AI features, also request `rails-rubyllm`. A direct skill folder URL works as a source, for example https://github.com/blahutka/skills/tree/main/skills/rails-rubyllm. In Codex, a skill installer can download GitHub skill folders; explicitly request the project-local destination because its default can be user-wide.

Installation means copying instructions, not adding gems, credentials, migrations, or application agents. Review conflicts before replacing an existing local skill. Skills should be available on the next turn; discovery and invocation syntax depend on the coding agent.

Expected destination:

```text
.agents/skills/
  rails-packs/SKILL.md
  rails-packs/references/pack-structure.md
  rails-servus/SKILL.md
  rails-servus/references/events.md
  rails-rubyllm/SKILL.md
  rails-rubyllm/references/invoice-review-example.md
```

Copy all files in each selected directory, not just those shown above. Commit the installed folders with the application so collaborators use the same version.

## Use and update

In Codex, invoke explicitly with `$rails-packs`, `$rails-servus`, or `$rails-rubyllm`; applicable skills can also be selected automatically. These are coding-agent instructions, not runtime agents or Ruby gems.

Keep project-specific versions, enabled features, commands, and required architecture in the project's AGENTS.md. Skills provide reusable workflows and examples; project instructions and explicit user choices take precedence. Do not paste the entire library into AGENTS.md.

For reproducible installations, select a commit SHA or an existing release tag instead of a moving main branch. Keep the source URL and commit in your project documentation, and review diffs before updating. These copied skills do not update automatically. No release tag is required to install from a commit.

## Add another skill

Create `skills/<name>/SKILL.md` with YAML frontmatter containing `name` and a precise `description`. Keep the entry point focused; put substantial conditional examples in linked `references/` files. Add scripts or assets only when they support a real workflow. Update the catalog above and validate frontmatter, local links, and any executable examples.

The folder format is based on the [Agent Skills standard](https://agentskills.io/specification). Repository-local discovery is documented in [Codex skills](https://developers.openai.com/codex/skills/). No marketplace registration is required for copying individual skill folders; this repository does not currently provide a Claude marketplace manifest.
