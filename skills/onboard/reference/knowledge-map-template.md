# Knowledge map structure

Two files, both repo-wide (like the glossary, not per-scope — see `reference/glossary-template.md`):

- **`docs/onboarding/knowledge-map.json`** — the structured source of truth. Language-agnostic (node IDs are slugs, not titles), so there's only ever one of this file, unlike the glossary/doc/FAQ which are per-`docLanguages` entry. Keep this clean and stable — it's meant to be consumable by a future dashboard tool, not just an intermediate for generating the Mermaid view below.
- **`docs/onboarding/knowledge-map.<lang>.md`** — a static Mermaid rendering generated *from* the JSON, one per configured doc language (titles are language-specific). This exists so the map is viewable on GitHub without any extra tooling; it is not hand-edited and is fully regenerated from the JSON each time.

## JSON schema

```json
{
  "nodes": [
    {
      "id": "<scope-slug>#<chapter-slug>",
      "scope": "<scope-slug>",
      "chapter": "<chapter title>",
      "understoodCount": 0,
      "uncertainCount": 0,
      "misunderstoodCount": 0
    }
  ],
  "termUsage": {
    "<glossary term>": ["<nodeId>", "<nodeId>"]
  }
}
```

- One node per chapter, across **all** scopes.
- `understoodCount`/`uncertainCount`/`misunderstoodCount`: anonymous running totals, incremented every time any user is graded on that chapter's quiz (`SKILL.md` step 4) — no user identifier attached, same principle as `completionCount`.
- `termUsage`: for every glossary term, the list of chapter node IDs that reference it (built incrementally whenever a chapter checks the glossary in step 4.2 — not just the term's "first introduced in" chapter). Two or more chapters sharing a term is what makes an edge. If a term has per-scope conflicting definitions (see `reference/glossary-template.md`'s "In <scope-slug>" sub-sections), don't link chapters across scopes that use *different* definitions of the same term name — only link chapters that genuinely share the same meaning. Track `termUsage` per definition (e.g. keyed by `<term>` for the default and `<term>@<scope-slug>` for a scope-specific override) so this distinction survives into the graph.

## Generating the Mermaid view

For each language's `knowledge-map.<lang>.md`, derive a Mermaid flowchart from the JSON:
- One node per chapter, labeled with its title and comprehension health, e.g. `Idempotency (62% understood)`.
- One edge between every pair of chapters that share a `termUsage` entry, labeled with that term.
- Style nodes below 50% understood distinctly (e.g. a `classDef` for a warning color) so weak spots are visually obvious without reading every label.

## Schema-version handling

Like `completionCount` (see `SKILL.md` step 7), the per-chapter counts and `termUsage` are real accumulated data, not regenerable from the codebase — carry them forward across a `schemaVersion` bump rather than resetting them. Only add new fields; don't wipe existing ones.

## Maintenance

Recompute both files whenever any chapter's quiz-grade counts change (i.e., right after step 4.6's grading) or when an orphaned scope is removed (`SKILL.md` step 1.3) — it's a cheap pure aggregation over existing `.meta.json` files and the glossary, not a fresh investigation.

## Stability

This JSON shape is **not yet a stable interface** — it's reusing this skill's existing `schemaVersion` mechanism (`SKILL.md` step 7) and may still change shape as the skill matures. A future dashboard tool should treat it the same way `/onboard` itself treats its own schema changes: expect to need updates when `schemaVersion` bumps, don't assume long-term field stability yet.

## Out of scope

A local dashboard server (Grafana-style) that reads `knowledge-map.json` and renders it interactively is an explicitly separate future product, not part of this skill — `/onboard` is a Claude Code skill (an instruction file), not a long-running application, and shouldn't try to become one. This skill's only obligation toward that future tool is keeping the JSON clean and stable.
