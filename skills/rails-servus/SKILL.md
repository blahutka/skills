---
name: rails-servus
description: "Implement or review Servus services and Rails controller integration, including event-driven workflows across domain packs. Use when a project selects or already uses Servus."
---

# Rails Servus

Use Servus for named actions and workflow boundaries when the project chooses it. Installation of this skill does not authorize adding the gem or migrating existing code.

## Workflow

1. Inspect installed Servus version, initializers, neighboring services, controllers, event classes, and package boundaries. Consult the matching official documentation before using APIs.
2. Give an action its own namespace with one Servus::Base subclass named Service, keyword inputs, a call method, and an explicit success/failure response. Keep support objects private to the action. Preserve business invariants in their owning domain models.
3. For a single controller service call, use run_service with permitted inputs. Check the returned result before rendering success; failure is already rendered. @result is available for response/view use. Default error rendering is JSON: adapt centrally for the application's HTML/Turbo conventions when needed.
4. For asynchronous reactions, read [event integration](references/events.md). Producers publish outcomes; receiving domains own their reaction logic. Root event wiring coordinates public receiving services without exposing private pack classes.
5. Verify response/error behavior and the actual queued workflow. Test event payloads, receiving behavior, retries, and relevant transaction boundaries using the configured test adapter.

## Boundaries

Do not add direct cross-pack mutations to bypass the event policy. Events do not return synchronous query results; agree on a public read contract or maintain an event-fed projection when needed. Do not assume publishing an event means a transaction committed or downstream work finished.

## Sources

- Services: https://zarpay.github.io/servus/core/service-objects
- Quick start: https://zarpay.github.io/servus/getting-started/
- Controllers: https://zarpay.github.io/servus/rails/controllers
- Events: https://zarpay.github.io/servus/features/event-bus

Use installed-version documentation if these pages describe a newer API.
