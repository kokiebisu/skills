# Corrections sidecar structure

Path: `docs/onboarding/<scope-slug>.corrections.md`. Human-maintained — this is the *only* file in the `/onboard` output set that people are expected to hand-edit. Read it back in during investigation (SKILL.md step 3) and incorporate every entry; never overwrite this file yourself.

```markdown
# Corrections: <Domain name>

## <Chapter title this correction applies to>

**Generated explanation said:** <short quote or paraphrase of what `/onboard` got wrong>
**Actually:** <the correct explanation>
**Added by:** <name/handle> on <date>
```

Rules:
- Each entry targets one chapter by its exact title, so it can be matched back up during regeneration.
- This file is for cases where the AI itself misread the code (not for routine doc staleness — that's handled automatically by the diff-based update and the doc/code conflict callouts, and doesn't need a human to write anything down).
- If a scope's corrections file accumulates multiple entries, flag this to the user during the tour as a possible bus-factor/tribal-knowledge risk signal worth raising with the team — don't just silently apply the corrections and move on.
- Don't delete entries automatically, even if a later regeneration seems to make them moot — let a human remove an entry once it's confirmed no longer needed.
- If two entries for the same chapter contradict each other, don't resolve the contradiction yourself (don't pick the newer one, don't re-investigate the code to settle it) — present both to the learner as an unresolved conflict, and suggest they confirm with the team. The correct fix is a human removing the stale entry, not Claude guessing which one wins.
