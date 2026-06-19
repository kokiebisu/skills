# FAQ structure

Path: `<scope-slug>.faq.<lang>.md`, one per scope (not shared across scopes, unlike the glossary — see `reference/glossary-template.md`). Captures domain/code questions the learner asks freely during a chapter (distinct from the per-chapter comprehension quiz). Entirely generated, same GENERATED-only rule as the main doc (`reference/doc-template.md`) — corrections go through `corrections-template.md`, not hand-edits here.

```markdown
# FAQ: <Domain name>

## <Question, in canonical phrasing>

<Concise, direct answer — not the two-layer analogy/precision structure used in chapters. Link to the relevant chapter for the full explanation instead of repeating it here.>

**Asked:** 4 times · **Related chapter:** [<chapter title>](./<scope-slug>.<lang>.md#<chapter-anchor>)

## <Next question>
...
```

Rules:
- Only record questions about the domain/code itself (e.g. "why does this retry 3 times?") — never operational remarks ("skip this", "explain again", "next") or small talk.
- Before answering a question live, check this file for an existing entry with the same underlying meaning (not exact string match — judge it the same way glossary term reuse is judged). If one exists, reuse its answer for consistency and just increment `Asked:`. If not, answer fresh and add a new entry with `Asked: 1`.
- The `Asked:` count is anonymous — no user identifier attached, ever.
- Don't announce a recording or count increment in the conversation as it happens — it surfaces naturally at the commit-confirmation step like any other file change.
- At 3+ asks, the chapter becomes a candidate for folding this question's answer directly into its explanation at the next diff-based regeneration (see `SKILL.md` step 7) — the FAQ entry itself isn't deleted when that happens, it just stops being the only place the answer lives.
- No size cap and no manual pruning — the fold-into-chapter mechanism above is the only de-growth mechanism, by design.
- If a recorded answer turns out to be wrong, the fix goes through the shared `<scope-slug>.corrections.md` (same file used for chapter mistakes — see `reference/corrections-template.md`), naming the FAQ question it corrects.
- When a scope is detected as orphaned (its directories no longer exist — see `SKILL.md` step 1), this file is deleted together with the rest of that scope's file set, with the same confirm-before-delete rule.
