# Event integration across domains

Producers declare outcomes with emits, for example `emits :billing_invoice_created_event, on: :success`. Servus emits after result validation; this does not itself guarantee database commit. The default success payload is result.data; explicitly shape a stable integration contract when the service result contains internal data.

Event classes are global, top-level classes in root app/events with an _event.rb suffix. One event name maps to one Event class. Add multiple enqueue declarations to that class for fan-out; do not register separate Event classes under the same name.

```ruby
# app/events/billing_invoice_created_event.rb
class BillingInvoiceCreatedEvent < Servus::Event
  enqueue Billing::ReviewInvoice::Service do |payload|
    { account_id: payload.fetch(:account_id), invoice_id: payload.fetch(:invoice_id) }
  end
end
```

Keep business logic out of the Event class. In a pack application, the receiving Service is a public entry point; it can access its own private domain code. Root wiring declares dependencies on receiving packs. Root app/events is an intentional orchestration exception to a pack-only domain logic policy.

Servus enqueue reactions always use ActiveJob. Queue order is not a completion-order guarantee. Keep consumers idempotent, and verify job adapter transaction behavior. Publish after commit or use a durable publication design when delivery reliability requires it; a simple after-commit callback alone is not a transactional outbox.

Define payload schemas when implementing integration contracts. Prefer identifiers, tenant identity, event identity, and versioned serializable data over ActiveRecord objects. Validate that Event files load at boot. If enabling require_event_payload_schema, existing emissions without a registered schema must be addressed first.

Test dispatch and enqueue behavior separately from receiving service behavior. Cover duplicate deliveries, authorization, rollback visibility, and downstream failure according to the workflow requirements. Optional extensions should not make the primary business operation depend on AI provider availability.
