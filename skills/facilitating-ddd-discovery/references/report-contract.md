# HTML Report Contract

Create two forms of the report from the same decision ledger.

## Living discovery report

Update it after material confirmations. Keep it useful during the interview, not merely attractive.

Required sections:

1. Topic, current outcome, and scope
2. Established facts
3. Candidate vocabulary
4. Actors and responsibilities
5. Current lifecycle or scenario flow
6. Business rules and boundary examples
7. Candidate decisions and their tradeoffs
8. Open questions, ordered by impact
9. The next decision to clarify

Use visible labels consistently:

- **Confirmed** — stated or explicitly accepted by the stakeholder
- **Candidate** — proposed wording or interpretation awaiting confirmation
- **Technical proposal** — implementation advice derived from the model
- **Open** — unresolved and capable of changing behavior or structure

Never display an inference as confirmed. Record superseded decisions without presenting them as current.

## Final report

After the readiness gate, transform the living report into this reading order:

1. Executive summary and desired outcome
2. Scope, non-goals, and consciously deferred questions
3. Vocabulary table with precise meanings and context
4. Actors, responsibilities, commands, and domain events
5. Primary scenarios and lifecycle/state transitions
6. Business rules with concrete boundary tables
7. Ownership, consistency boundaries, and bounded-context candidates
8. User-facing implications
9. Business statement ↔ proposed API mappings
10. Project-aware code examples and folder structure
11. Remaining risks and open decisions

Technical sections must remain visually marked as proposals. Do not imply that stakeholder approval of business behavior approves a particular persistence or class design.

## Visual and file requirements

- Produce one valid, standalone HTML5 file with inline CSS and no remote dependencies.
- Use semantic headings, tables, lists, code blocks, and accessible color contrast.
- Include a compact navigation area for long reports and responsive styles for narrow screens.
- Include print styles; do not rely on hover to reveal meaning.
- Prefer compact flow diagrams made with HTML/CSS or inline SVG; avoid external diagram libraries.
- Omit empty sections instead of leaving placeholders.
- Replace every `{{TOKEN}}` from the template before delivery.
- Follow workspace artifact-location instructions. If none exist, ask once before writing the first report.
- Verify the rendered HTML in a browser when browser tools are available.

## Code examples

Code should illustrate the agreed language rather than complete an unapproved implementation.

For every example, state:

- the confirmed business statement it represents;
- which parts are technical proposals;
- any unresolved decision that could change the API.

When a project is available, inspect nearby code and follow its naming and folder conventions. Otherwise keep examples small and clearly illustrative.

