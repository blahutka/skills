---
name: rails-packs
description: "Design, implement, or review Rails modular monoliths using packs-rails, Packwerk, and automatic_namespaces by default. Use for pack structure, public interfaces, dependencies, and autoloading."
---

# Rails Packs

Organize code by business ownership while preserving Rails conventions. These are reusable defaults; inspect the project's AGENTS.md, installed gems, package files, and neighboring packs first. Do not reorganize an existing application merely because this skill is installed.

## Workflow

1. Inspect Gemfile/Gemfile.lock, root and pack package.yml files, config/routes.rb, and representative models/controllers/specs. Identify actual dependency/privacy tooling and namespace configuration.
2. Choose the meaningful domain that owns the behavior. Extend an existing pack before creating a new one for a few unrelated files.
3. Keep domain implementation private. Put deliberate entry points and contracts in the configured public path; declare dependencies in the package that references those constants. A dependency does not override privacy.
4. Follow the application's rich model/domain object conventions. Keep HTTP and job entry points thin. Use services where the application already has a meaningful action/service pattern.
5. Default new namespaced packs to `automatic_namespaces`: add the gem when implementing pack setup, opt the pack in with `metadata.automatic_pack_namespace: true`, and omit redundant domain folders under Ruby autoload roots. Keep explicit Ruby modules/classes and namespaced routes. Preserve existing layouts unless migration is requested; follow an explicit project opt-out. Installing this skill alone does not change application dependencies.
6. For layout, namespace decisions, and worked Ruby examples (models, controllers, actions, public contracts, routes, and specs), read [pack structure](references/pack-structure.md). Check installed tooling before assuming a configuration key is supported.
7. Verify Rails autoloading, model table names, development reloading after namespace changes, and relevant behavior tests. Run configured dependency/privacy checks when present. Report missing tooling honestly; package.yml alone does not prove boundaries are enforced.

## Integration choices

Use the project's communication policy. If it uses Servus, preserve event-based cross-pack reactions and public receiving services. Synchronous queries need an explicit public contract; an asynchronous event is not a query return value. Do not install Servus or RubyLLM as a side effect of this skill.

Keep project versions, required conventions, and verification commands in AGENTS.md. Avoid copying the whole skill into it.

## Sources

- packs-rails: https://github.com/rubyatscale/packs-rails
- Packwerk: https://github.com/Shopify/packwerk
- automatic_namespaces (default for new namespaced packs): https://github.com/Flytedesk/automatic_namespaces

Consult the documentation matching the project's installed versions before changing configuration.
