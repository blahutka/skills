---
name: rails-rubyllm
description: "Build or review optional AI features in Rails with RubyLLM: domain-owned agents, tools, structured assessments, and background execution. Use only when AI functionality is requested or already exists."
---

# Rails RubyLLM

Add AI behavior only when a feature needs it. Installing this skill does not add gems, providers, credentials, migrations, or application agents.

## Ownership

Keep each agent, its prompts, tools, output contracts, and domain results in the owning domain: billing in billing, pricing in pricing. In a packs application use that pack's app/agents and app/tools only after verifying autoloading and namespace mapping. Verify prompt discovery separately; a conventional app/prompts lookup may not discover pack templates automatically.

Do not create a generic ai_agents pack for domain-specific reasoning. Extract shared run tracking, cost reporting, or execution policy only when there is demonstrated reuse. A configurable assistant platform can be its own domain if the product actually requires one.

## Workflow

1. Inspect Ruby/Rails and RubyLLM versions, provider configuration, domain authorization, and existing jobs/services. Check version compatibility before adding dependencies within the requested scope.
2. Define the smallest agent with focused instructions, only necessary tools, and a structured response when domain code consumes it. RubyLLM::Agent encapsulates configuration; a simple structured chat can be enough for one isolated task.
3. Keep model reasoning separate from domain authority. Validate output before saving results; deterministic domain code applies permitted state changes. Treat supplied documents and tool results as untrusted data.
4. Execute long-running work through the existing background workflow. With Servus, expose the receiving service through the pack's public boundary and wire optional reactions through a root Event class. Do not install Servus just to run an agent; an ordinary Rails job also works.
5. Scope tool instances to the authorized user/account in trusted application code. Do not let model-supplied identifiers establish permissions. Cross-domain reads need an approved public query or local projection; events cannot synchronously return a tool result.
6. Persist run status, triggering request/event identity, input version, model/prompt version, and result when execution needs retries or auditability. Deduplicate delivery, reject stale results, and set execution/cost limits appropriate to the task. Distinguish retryable provider failures from invalid domain results.
7. Verify workflow behavior with stubbed responses: success, invalid output, failure, retries, stale data, and tenant isolation. Use representative evaluations separately to assess reasoning quality; do not make routine tests call paid providers.

For a concrete Servus integration, read [invoice review example](references/invoice-review-example.md). It is a teaching example, not a production scaffold.

## Sources

- Setup and compatibility: https://rubyllm.com/getting-started/
- Agent APIs and prompt conventions: https://rubyllm.com/agents/
- Tools: https://rubyllm.com/tools/
- Structured output: https://rubyllm.com/structured-output/

Match examples to the installed RubyLLM version. Do not assume agent APIs exist in older releases.
