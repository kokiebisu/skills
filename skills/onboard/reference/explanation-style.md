# Explanation style

How to explain each chapter's domain concept, not just what to cover.

## Calibrate by complexity, don't force a template

Not every chapter needs the full treatment. A simple fact ("there are 3 order statuses") just needs to be stated plainly. Reserve the techniques below for concepts that are abstract, counter-intuitive, or where new hires typically get confused. Forcing analogies/diagrams/scenarios onto trivial chapters makes the tour slower and reads as padding.

## One-time calibration, not continuous adaptation

At the start of a fresh tour (see SKILL.md step 1), the learner answers one question about their familiarity with concepts likely to recur in this scope. Use that answer to set how much hand-holding this tour defaults to for the whole scope (e.g. an experienced hire who says they already know event-driven systems gets lighter treatment of those concepts than someone who says they don't). This is a single initial setting, not something re-evaluated per chapter or adjusted based on how a single quiz answer goes — don't build continuous/per-chapter adaptive logic here.

**One exception — a single escalation, not continuous tuning:** self-reported familiarity is unreliable (people overestimate what they know). If this user scores `misunderstood` on two chapters in a row within the same tour, increase the hand-holding level once, with a brief acknowledgment ("I'd set this to a lighter explanation style based on what you told me earlier — let me be more thorough from here"). This is a one-shot step up triggered by a clear threshold, not ongoing per-chapter re-tuning, and it only ever escalates (never silently dials back down to lighter again within the same tour).

## Don't pad — techniques compress, they don't add length

Aim for each chapter to be readable in about as much time as it'd take to explain out loud in a couple of minutes. Using an analogy/diagram/scenario should make the same information easier to grasp, not add extra length on top of a full explanation — keep the analogy itself to a sentence or two, the scenario to its essential steps, the diagram to the minimum boxes/arrows needed. If you notice a chapter running long, cut elaboration before cutting the precision layer.

## Don't force one theme across the whole tour

Pick whichever analogy fits each concept best, chosen independently per chapter — don't try to reuse one extended metaphor (e.g. "everything is a kitchen") across the whole tour. Concepts vary enough that forcing one theme onto all of them produces more awkward, strained analogies than freely choosing per concept. (Cross-chapter consistency for *recurring terms* is handled separately by the shared glossary — see `reference/glossary-template.md` — not by a unifying analogy theme.)

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
