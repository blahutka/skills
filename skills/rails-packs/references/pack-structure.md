# Pack structure and namespace checks

A typical domain pack has package.yml plus app/models, app/controllers, app/views, and local specs. Add app/services, app/agents, or app/tools only when the feature uses them. Keep database migrations in the application's db/migrate unless the existing application explicitly manages them differently.

A common policy is enforce_dependencies: true, enforce_privacy: true, with public_path: app/public/. Confirm installed tools support these keys and actually run privacy checks. Root package settings and shared packs are project decisions; do not silently disable enforcement to make a reference pass.

Without automatic_namespaces, use normal Zeitwerk paths, for example app/models/billing/invoice.rb for Billing::Invoice. With automatic_namespaces configured for a pack, app/models/invoice.rb can define Billing::Invoice. Inspect namespace exclusions and existing code before applying this to controllers or other roots. Do not duplicate a namespace directory or assume a new autoload root has inherited the desired mapping.

Routes are not proven to load merely because a pack has config/routes.rb. Follow existing explicit route loading and verify the resulting routes. Likewise check factory/spec discovery and template lookup rather than assuming they follow model autoloading.

Public paths expose contracts, not all implementation. A root event coordinator referencing a public receiving service must declare its dependency where enforcement is enabled. Another pack must not import private models even after declaring a dependency.

Run bundle exec rails zeitwerk:check and focused specs when the application supports them. Use the repository's configured Packwerk/privacy commands; verify their availability before prescribing a command.

## Worked example: billing pack

This example uses ordinary Zeitwerk namespace directories, without automatic namespace rewriting. It assumes Rails, packs-rails, PostgreSQL, RSpec, and FactoryBot are already configured. Adapt dependencies and test setup to the application; installing the skill does not install these gems.

```text
packs/billing/
  package.yml
  app/
    models/billing.rb
    models/billing/invoice.rb
    controllers/billing/invoices_controller.rb
    services/billing/create_invoice.rb
    public/billing/invoice_summary.rb
    views/billing/invoices/index.html.erb
  config/routes.rb
  spec/
    factories/billing_invoices.rb
    models/billing/invoice_spec.rb
    services/billing/create_invoice_spec.rb
    requests/billing/invoices_spec.rb
```

### Package contract

```yaml
# packs/billing/package.yml
# Privacy keys require a compatible privacy checker in addition to Packwerk.
enforce_dependencies: true
enforce_privacy: true
public_path: app/public/
dependencies:
  - '.'
```

Declare the root dependency because these examples inherit ApplicationRecord and ApplicationController. When another package references the public Billing::InvoiceSummary contract, that caller declares `packs/billing` in its own dependencies. This does not permit private model access.

### Model and migration

```ruby
# packs/billing/app/models/billing.rb
# For this conventional layout; reuse an existing Billing namespace if present.
module Billing
  def self.table_name_prefix
    "billing_"
  end
end
```

```ruby
# packs/billing/app/models/billing/invoice.rb
class Billing::Invoice < ApplicationRecord
  validates :number, presence: true, uniqueness: true
  validates :total_cents, numericality: { only_integer: true, greater_than: 0 }
end
```

```ruby
# db/migrate/<timestamp>_create_billing_invoices.rb
# Use the migration version generated for the actual Rails application.
class CreateBillingInvoices < ActiveRecord::Migration[8.1]
  def change
    create_table :billing_invoices do |t|
      t.string :number, null: false
      t.integer :total_cents, null: false
      t.timestamps
    end
    add_index :billing_invoices, :number, unique: true
    add_check_constraint :billing_invoices, "total_cents > 0",
      name: "billing_invoices_positive_total"
  end
end
```

The Billing namespace above supplies the billing_ table prefix. Reuse existing namespace configuration instead of redefining it; with automatic_namespaces, verify its namespace setup or configure the model table name explicitly. For multi-tenant billing, scope invoice lookup and uniqueness to the account and use a matching composite unique index.

### Controller, routes, and view

```ruby
# packs/billing/app/controllers/billing/invoices_controller.rb
class Billing::InvoicesController < ApplicationController
  def index
    @invoices = Billing::Invoice.order(created_at: :desc)
  end
end
```

```ruby
# packs/billing/config/routes.rb
namespace :billing do
  resources :invoices, only: :index
end
```

If the application does not already load pack route files, one possible root loader is:

```ruby
# config/routes.rb — inside the existing draw block
Rails.application.routes.draw do
  Dir.glob(Rails.root.join("packs/*/config/routes.rb")).sort.each do |path|
    instance_eval(File.read(path), path)
  end
end
```

Merge this with existing routes; do not replace the application's routing configuration. Confirm routes reload as expected in development.

```erb
<%# packs/billing/app/views/billing/invoices/index.html.erb %>
<h1>Invoices</h1>
<% @invoices.each do |invoice| %>
  <p><%= invoice.number %>: <%= invoice.total_cents %> cents</p>
<% end %>
```

The controller intentionally omits project-specific authentication, account scoping, and pagination; apply those before using it in a real application.

### Private action and public read contract

For projects with a plain action-object convention:

```ruby
# packs/billing/app/services/billing/create_invoice.rb
class Billing::CreateInvoice
  def self.call(number:, total_cents:)
    Billing::Invoice.create!(number: number, total_cents: total_cents)
  end
end
```

Keep this private to billing. For projects using Servus, follow their namespaced Service convention instead; do not introduce parallel action conventions. After commit, publish business outcomes through the existing event workflow rather than calling another pack's mutation directly.

A synchronous read can expose a narrow contract when the project's communication policy permits it:

```ruby
# packs/billing/app/public/billing/invoice_summary.rb
class Billing::InvoiceSummary
  def self.fetch(id:)
    invoice = Billing::Invoice.find(id)
    { id: invoice.id, number: invoice.number, total_cents: invoice.total_cents }
  end
end
```

This deliberately returns plain data rather than the private ActiveRecord object. Add trusted account/user authorization when required. In event-only integrations, use payloads or local projections instead of introducing this query contract.

### Factory and behavior specs

```ruby
# packs/billing/spec/factories/billing_invoices.rb
FactoryBot.define do
  factory :billing_invoice, class: "Billing::Invoice" do
    sequence(:number) { |n| "INV-#{n}" }
    total_cents { 12_500 }
  end
end
```

```ruby
# packs/billing/spec/models/billing/invoice_spec.rb
require "rails_helper"

RSpec.describe Billing::Invoice do
  it "rejects a non-positive total" do
    invoice = build(:billing_invoice, total_cents: 0)
    expect(invoice).not_to be_valid
    expect(invoice.errors[:total_cents]).not_to be_empty
  end
end
```

```ruby
# packs/billing/spec/services/billing/create_invoice_spec.rb
require "rails_helper"

RSpec.describe Billing::CreateInvoice do
  it "persists the requested invoice" do
    invoice = described_class.call(number: "INV-42", total_cents: 12_500)
    expect(invoice.reload.attributes).to include(
      "number" => "INV-42", "total_cents" => 12_500
    )
  end
end
```

```ruby
# packs/billing/spec/requests/billing/invoices_spec.rb
require "rails_helper"

RSpec.describe "Billing invoice listing", type: :request do
  it "shows the invoice number" do
    invoice = create(:billing_invoice)
    get billing_invoices_path
    expect(response).to have_http_status(:ok)
    expect(response.body).to include(invoice.number)
  end
end
```

Ensure rails_helper includes FactoryBot syntax and discovers pack factory files. If the installed packs-rails version provides an RSpec helper, use its documented setup; otherwise add an explicit factory path and appropriate spec loading. Adapt request specs to the actual authentication policy.

### When automatic_namespaces is enabled

Only with the gem installed and its configuration verified, a models directory can omit the redundant domain folder:

```ruby
# packs/billing/app/models/invoice.rb
class Billing::Invoice < ApplicationRecord
  validates :number, presence: true
end
```

A commonly used pack metadata configuration is:

```yaml
metadata:
  automatic_pack_namespace: true
  automatic_pack_namespace_exclusions:
    - controllers
    - views
    - assets
    - helpers
    - javascript
```

Here controllers are explicitly excluded so the conventional controllers/billing/... paths above remain appropriate. Inspect the installed version's behavior for public, services, and any new roots as well; adjust their paths consistently rather than blindly copying the conventional tree. Verify with Zeitwerk and route/request checks before adopting this variant.

### Focused verification

```sh
bundle exec rails zeitwerk:check
bundle exec rails routes -g billing
bundle exec rspec packs/billing/
```

Also run the application's configured dependency/privacy checks. The examples are reference snippets, not a complete generated Rails application.
