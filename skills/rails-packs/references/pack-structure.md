# Pack structure and namespace checks

A typical domain pack has package.yml plus app/models, app/controllers, app/views, and local specs. Add app/services, app/agents, or app/tools only when the feature uses them. Keep database migrations in the application's db/migrate unless the existing application explicitly manages them differently.

A common policy is enforce_dependencies: true, enforce_privacy: true, with public_path: app/public/. Confirm installed tools support these keys and actually run privacy checks. Root package settings and shared packs are project decisions; do not silently disable enforcement to make a reference pass.

Without automatic_namespaces, use normal Zeitwerk paths, for example app/models/billing/invoice.rb for Billing::Invoice. With automatic_namespaces configured for a pack, app/models/invoice.rb can define Billing::Invoice. Inspect namespace exclusions and existing code before applying this to controllers or other roots. Do not duplicate a namespace directory or assume a new autoload root has inherited the desired mapping.

Routes are not proven to load merely because a pack has config/routes.rb. Follow existing explicit route loading and verify the resulting routes. Likewise check factory/spec discovery and template lookup rather than assuming they follow model autoloading.

Public paths expose contracts, not all implementation. A root event coordinator referencing a public receiving service must declare its dependency where enforcement is enabled. Another pack must not import private models even after declaring a dependency.

Run bundle exec rails zeitwerk:check and focused specs when the application supports them. Use the repository's configured Packwerk/privacy commands; verify their availability before prescribing a command.
