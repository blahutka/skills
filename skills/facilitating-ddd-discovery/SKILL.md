---
name: facilitating-ddd-discovery
description: Use when stakeholder requirements, business vocabulary, workflows, rules, ownership, or boundaries are unclear and must be discovered before writing a DDD specification or coding API.
---

# Facilitating DDD Discovery

## Core principle

Discover the stakeholder's model before proposing the technical model. Never turn a vague brief into aggregates, states, APIs, or folders by silently filling gaps.

## Conversation contract

Work on one decision at a time.

1. Start a new area with one open question about a concrete real-world scenario.
2. Play back the answer as:
   - **Confirmed:** stated or explicitly accepted by the stakeholder.
   - **Candidate:** vocabulary or interpretations under consideration.
   - **Technical proposal:** an implementation idea, never a business fact.
   - **Open:** a decision still affecting behavior or structure.
3. Expose the most consequential ambiguity.
4. After hearing the stakeholder's language, offer 2–3 concrete interpretations, recommend one with reasons, and permit a different answer.
5. Test the choice with boundary examples and counterexamples.
6. Confirm the result before advancing.

Do not begin with multiple choice: early options make the agent author the domain. Use options to resolve an ambiguity already revealed by the stakeholder's account.

## Discovery loop

For the current scenario, uncover only what matters:

- actor and goal;
- trigger or command;
- required starting state;
- rules, permissions, and invariants;
- rejection and failure cases;
- resulting state and observable domain event;
- terminology used by customers, operations, and support;
- owner of the decision and context in which each term is valid.

Ask for examples near thresholds: exactly at a deadline, between stated ranges, repeated actions, concurrent changes, missing data, and reversal. Never invent behavior for a gap.

When AI participates, distinguish **AI suggestion**, **human-confirmed decision**, and **automated consequence**. Ask who has final authority and what happens when AI is uncertain or unavailable.

Maintain the living HTML report after each material confirmation. Read [references/report-contract.md](references/report-contract.md) before creating or updating it, and adapt [assets/ddd-report-template.html](assets/ddd-report-template.html).

## Readiness gate

Do not translate the model into code while material questions remain hidden.

When vocabulary, lifecycle, rules, ownership, and boundaries are sufficiently clear:

1. Summarize confirmed decisions.
2. List unresolved decisions and their impact.
3. Say discovery appears ready for technical translation.
4. Ask for explicit approval to finalize.

Document consciously deferred questions; they do not prevent finalization when their impact is explicit and the stakeholder accepts the deferral.

## Technical translation

Only after approval:

- Inspect the existing project vocabulary and structure when available.
- Pair each proposed API with the confirmed business statement it expresses.
- Label APIs, persistence, events, endpoints, and folders as technical proposals.
- Do not let database or framework names silently replace agreed domain terms.
- For Rails code or folder design, **REQUIRED SUB-SKILL:** use `rails-domain-modeling`.
- Prefer domain-owned behavior over generic `Service`, `Manager`, `Handler`, or `Processor` names unless project language justifies them.

Generate the polished standalone HTML report only after this gate.

## Example questioning move

Stakeholder: “Support approves the sitter profile.”

Ask first: “Walk me through what support approves and what the sitter experiences afterward.”

After the stakeholder reveals multiple service profiles, offer interpretations: approval may belong to the sitter, one service profile, or a specific submitted version. Recommend only after explaining the consequences, then test whether one profile can be approved while another needs corrections.

## Red flags

- Producing a class diagram or API in the first reply to a vague brief.
- Treating an agent suggestion as stakeholder-confirmed.
- Asking several unrelated questions at once.
- Using one happy-path example without boundary cases.
- Calling discovery complete without showing unresolved decisions.
- Presenting framework structure as though the stakeholder requested it.
