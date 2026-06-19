# Explanation style

How to explain each chapter's domain concept, not just what to cover.

## Calibrate by complexity, don't force a template

Not every chapter needs the full treatment. A simple fact ("there are 3 order statuses") just needs to be stated plainly. Reserve the techniques below for concepts that are abstract, counter-intuitive, or where new hires typically get confused. Forcing analogies/diagrams/scenarios onto trivial chapters makes the tour slower and reads as padding.

## Three techniques, chosen per concept — not a fixed 3-piece template

For a concept that needs more than a plain statement, pick whichever of these actually fits it — use one, two, or all three, but don't mechanically apply all three to every chapter:

- **Analogy**: best for a concept that's counter-intuitive or being heard for the first time — something the learner needs an intuitive "click" for (e.g. eventual consistency, idempotency).
- **Diagram**: best for a concept that has *structure* — a state machine, a data flow, a sequence of calls. Use simple ASCII/box-and-arrow diagrams when talking in the conversation (these render correctly in any terminal). When the same diagram is written into the generated doc (`docs/onboarding/<scope-slug>.<lang>.md`), convert it to a Mermaid code block instead — GitHub renders Mermaid natively, so the committed doc looks right when teammates browse it there later. Don't use Mermaid in the live conversation; it won't render as a diagram there.
- **Concrete scenario**: best for a concept that's procedural / timing-sensitive — walk through one specific, concrete instance end to end (e.g. "let's trace what happens to order #1234 from creation to fulfillment") rather than describing the general case abstractly.

## Two-layer structure

For any chapter using the techniques above, structure the explanation in two layers:
1. **Intuitive layer**: the analogy/diagram/scenario, in plain language, to build a correct mental model first.
2. **Precision layer**: immediately after, the exact technical statement — correct terminology, the actual invariant, and the code references. Introduce this with something like "technically speaking:" or "in this codebase's terms:".

Never skip the precision layer — the generated doc is a team asset (see `reference/doc-template.md`) that later readers need to be able to rely on for exact details, not just a friendly first impression.

## If the user says they still don't understand

1. First, switch to a *different kind* of technique than the one you used (e.g. you led with an analogy — try a concrete scenario walkthrough next, not a rephrased version of the same analogy). Different people click with different techniques.
2. If that still doesn't land, ask the user to point at specifically which part is unclear, and address just that part rather than re-explaining the whole chapter again.
