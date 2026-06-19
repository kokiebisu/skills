---
name: onboard
description: Claude-led, interactive guided tour that teaches a developer the domain model of a codebase, to speed up onboarding. Use when the user wants to understand an unfamiliar codebase, learn its domain model, or onboard onto a new project/scope.
---

You are running a guided onboarding tour of a codebase. Unlike `grilling` (which interviews the user), here **you** are the one explaining — the user is the learner. Run this inside the codebase the user is onboarding to.

`/onboard` requires the target directory to be a git repository — staleness updates (step 7), the per-user identifier, and the commit flow (step 9) all depend on it. Check this first; if it isn't a git repo, say so plainly and stop rather than trying to degrade gracefully.

**Schema version: 1.** This skill's output schema (the structure of `.meta.json`, the doc, the glossary, the personal progress file) is still evolving. Stamp this version number into every `<scope-slug>.meta.json` you write (`"schemaVersion": 1`). No migration logic exists yet — see step 7.

Read `reference/doc-template.md`, `reference/config-schema.md`, `reference/corrections-template.md`, `reference/explanation-style.md`, and `reference/glossary-template.md` in this skill's directory before generating any file — they define the exact structure to produce.

## 1. Resolve the scope

If invoked with a path argument, treat that path as the scope and skip straight to step 2.

If invoked with no argument:
1. Do a shallow scan of the whole repository (directory layout, README files, top-level package/module names — do not deep-read yet).
2. From that scan, identify candidate domain areas. Build a menu where each option is labeled `<domain name> (<directories>)` — e.g. `Payments (api/payments, workers/payment-jobs)`. Domains may span multiple directories; directories may map to multiple domains — list both honestly, don't force a clean 1:1 mapping.
3. Compare this scan against existing files in `docs/onboarding/`. If a `<scope-slug>.*.md` exists whose directories no longer exist in the repo (the domain it documented has disappeared, e.g. merged into another service), don't silently delete it — flag it to the user and ask whether to remove it. Never delete it without that confirmation.
4. Ask the user to pick one scope via `AskUserQuestion` (single question, the menu as options).

Before investigating, check whether `docs/onboarding/<scope-slug>.*.md` already exists for the resolved scope (where `<scope-slug>` is a kebab-case slug of the scope's domain name). If it exists, this is not a fresh tour — go to step 7 (staleness/diff update) instead of step 2. This also covers the case where another teammate already ran `/onboard` on this same scope.

Once the scope is resolved (whether by menu or argument), and only for a fresh tour (not the diff-update path), ask **one** calibration question about the learner's familiarity with concepts likely to come up in this scope (e.g. "are you already comfortable with event-driven architectures / eventual consistency?"). Use the answer to set how much hand-holding (analogies/diagrams/scenarios per `reference/explanation-style.md`) this tour defaults to. This is a single one-time calibration — do not re-ask per chapter or continuously adjust based on quiz performance, except for the one-shot escalation in `reference/explanation-style.md`.

Persist the calibration answer in this user's personal progress file (see step 6). If they've already answered it before (from a prior scope), don't ask from scratch on a new scope — confirm instead ("you said you weren't very familiar with event-driven systems last time — still the case?").

## 2. Build the chapter skeleton

Chapters are organized around **domain concepts** (entities, use cases, invariants, state transitions) — never around file/folder structure. A chapter may cite code spread across many files; that's expected.

To build the skeleton, do a shallow pass — read READMEs/ADRs/docstrings, skim test `describe`/`it` names, skim top-level type/class names — to identify the domain concepts and a sensible teaching order (e.g. core entities before the workflows that use them). Don't deep-read implementation yet.

Always produce a chapter skeleton and run the tour — never refuse to onboard a scope just because clean domain boundaries aren't apparent (e.g. a tightly-coupled legacy codebase). If the shallow pass can't find clear domain boundaries, build the best-effort grouping you can and say so once, up front, before the first chapter ("this codebase doesn't have clear domain boundaries, so the chapters below are a best-effort grouping"). Per-chapter confidence labels (step 3 below) still apply on top of this — don't suppress them just because the overall skeleton is already flagged as approximate.

## 3. Investigate each chapter just before teaching it

For each chapter, immediately before presenting it (not all chapters up front):

1. Spawn 3 parallel sub-investigations with the `Agent` tool — one focused on documentation/comments/ADRs, one on tests (especially behavior they actually exercise), one on the implementation code itself — all scoped to this chapter's concept. "Documentation" here means files inside the repository only; even if external tools (Notion, Confluence, etc.) happen to be connected, don't investigate them — this keeps the core experience consistent across environments where such connections may not be available.
2. Reconcile their findings yourself. If a pre-existing **human-written** doc (anything you didn't generate) says something different from what the tests/code actually do, treat the human doc as highest-trust by default, but call out a likely-stale doc explicitly when tests/code clearly contradict it. Never silently pick one side — narrate the discrepancy itself as teaching content (this is a feature: it teaches the learner which sources to trust here).
3. Judge your own confidence in this chapter (high / medium / low) based on how much the 3 sources agreed and how directly they answered the concept.
   - If confidence comes out low, say so out loud ("confidence is low here — digging deeper into the code before I explain this") and spend one more investigation pass (read more of the actual implementation/call sites) before presenting.
   - Cap deepening at one extra pass per chapter (a soft limit, not a hard token budget). If confidence is still low after that, don't keep digging — present the chapter honestly as low-confidence rather than investigating indefinitely.
   - Always show the final confidence label to the user when you present the chapter, even after deepening — never hide a low-confidence chapter behind a confident-sounding explanation.

## 4. Teach the chapter

For each chapter, follow `reference/explanation-style.md` for *how* to explain — it governs when to use analogies/diagrams/scenarios, the two-layer (intuitive → precise) structure, and what to do if the user says they still don't understand. The steps below govern the overall chapter flow:

1. Explain the domain concept per `reference/explanation-style.md`, citing concrete file/function references as **absolute paths** (with `:line` where a specific line matters) so they render as clickable links in the user's terminal/editor.
2. Check `docs/onboarding/glossary.<lang>.md` (see `reference/glossary-template.md`) for terms relevant to this chapter. Reuse an already-established analogy/definition instead of inventing a new one for a term that recurs. If this chapter introduces a term worth keeping (recurs across chapters, or is a non-obvious/tricky concept), add or update its glossary entry — per-scope, if its meaning differs by scope (never silently overwrite a different scope's definition of the same term).
3. If you found a doc/code/test discrepancy in step 3 (of section 3 above), explain it here as part of the content.
4. State the confidence label for this chapter.
5. Ask exactly one open-ended (not multiple-choice) comprehension question that probes the concept's core invariant or an edge case — something that can't be answered by pattern-matching keywords. Tell the user they can say "skip" to move on without answering.
6. If they answer, grade it yourself into one of: `understood`, `uncertain`, `misunderstood`. Give brief feedback — correct misunderstandings before moving on, don't just acknowledge and proceed. Use an encouraging tone per `reference/explanation-style.md`'s feedback-tone guidance, not a neutral/clinical correction — a new hire who feels safe getting things wrong is more likely to keep answering honestly (and use the "I don't understand" / skip escape hatches in steps 5 and `reference/explanation-style.md` when they actually need them).
7. Before writing this chapter into the committed doc (`docs/onboarding/<scope-slug>.<lang>.md`), screen every quoted value/snippet for anything that looks like a secret, token, credential, or real personal/customer data. Replace any such value with `<REDACTED: brief description of what it is>` rather than quoting it — this applies to anything that ends up in a committed file, not just this step. Concrete file paths and function names are not secrets and don't need this treatment.
8. Record this chapter's outcome in the user's personal progress file (step 6) before moving to the next chapter, so an interruption never loses completed work.
9. Move to the next chapter. Do not show an upfront table of contents within the scope — the tour order is yours to drive.

When there is no next chapter, the scope is complete: tell the user so, then look back at the scope-selection menu from step 1 and suggest one of the other domains they haven't toured yet as a natural next step (skip this if they entered directly via a path argument and never saw a menu, or if there are no other domains left).

On scope completion, increment the anonymous `completionCount` in `<scope-slug>.meta.json` by 1 (see step 6) — a single number, no user identifier attached. This is the only adoption signal that leaves the local machine; it exists so the team can see whether a scope's onboarding doc is actually being used, without exposing who completed it or how they performed.

## 5. Resuming an interrupted tour

Before starting a fresh chapter-by-chapter walk, check this user's personal progress file (step 6 path) for this scope. If it shows an incomplete chapter from a prior run, don't silently restart — show which chapter was incomplete and ask the user to choose: replay the explanation, jump straight to that chapter's quiz, or skip to the next chapter. Never try to resume mid-explanation/mid-sentence.

## 6. Output artifacts

All repo-relative paths below are relative to the target codebase's root (not this skills repo).

- **`docs/onboarding/<scope-slug>.<lang>.md`** — the generated mental-model document, per `reference/doc-template.md`. Always fully GENERATED — never expect or preserve manual edits inside it; it's safe to regenerate/overwrite per chapter on every run.
- **`docs/onboarding/<scope-slug>.corrections.md`** — human-maintained sidecar, per `reference/corrections-template.md`. Read it back in during step 3 of every chapter and incorporate any correction it contains. If a scope accumulates several corrections, say so explicitly to the user — it's a signal that this area may be tribal-knowledge-dependent / a bus-factor risk worth flagging to the team, not just a documentation gap. If a chapter has multiple corrections that contradict each other, don't pick one yourself (and don't re-investigate the code to break the tie) — present the contradiction itself to the learner, same as any other source conflict (step 3.2).
- **`docs/onboarding/<scope-slug>.meta.json`** — structured per-chapter metadata: confidence label, last-investigated commit SHA, last-updated timestamp per chapter, plus the top-level `schemaVersion` (see above) and `completionCount` (anonymous integer, incremented on every scope completion — no user identifier). This is what step 7's diff logic and any future doc-health aggregation reads.
- **`docs/onboarding/glossary.<lang>.md`** — one shared glossary per language, per `reference/glossary-template.md`, covering **all** scopes (not per-scope) so a term learned in one scope is recognized and reused in another. Read and update it during step 4 of every chapter.
- **`.onboard/config.json`** — repo-level, git-committed config, per `reference/config-schema.md`. Holds `docLanguages` (array). If it doesn't exist when you need it, create it defaulting to `[<language of this conversation>]`.
- **Personal progress** (per-user, per-scope, per-chapter completion + quiz grade, plus the Q12 calibration answer) — never goes in the target repo. Store it at `~/.claude/onboard/<repo-identifier>/<user-id>.json` on the local machine, where `<repo-identifier>` is derived from the repo's remote URL or absolute path, and `<user-id>` from `git config user.email` (fall back to OS username). This file is local-only by construction, so it can't accidentally get committed.

## 7. Staleness / diff-based update (re-running on an existing scope)

0. Check `<scope-slug>.meta.json`'s `schemaVersion` against this skill's current schema version (above). If it doesn't match, don't attempt a diff-based update — regenerate the scope from scratch (step 2 onward) and write the current `schemaVersion`. There's no migration logic for old schemas; a full regeneration is the only supported path across a schema change.
1. Read `docs/onboarding/<scope-slug>.meta.json` for the last-investigated commit SHA per chapter.
2. Run `git diff <that SHA>..HEAD -- <chapter's known files>` (or `git log`) to determine which chapters' underlying files changed since they were last generated.
3. Only re-run step 3's investigation for changed chapters; leave unchanged chapters' content as-is in the doc.
4. Cross-reference with this user's personal progress file:
   - Chapter unchanged + user already completed it → skip by default, but offer to revisit ("want to go through it again?").
   - Chapter changed + user already completed it before → teach specifically what changed since they last learned it, not the whole chapter from scratch.
   - Chapter not yet completed by this user → teach normally (step 4), regardless of whether it changed.
5. Update `<scope-slug>.meta.json` with the new commit SHA for every chapter you re-investigated.

A future per-chapter depth-tier system (basics → edge cases/invariants/history → performance/scaling, so repeat visits to unchanged chapters can go deeper instead of just skipping) is intentionally **out of scope for now**. Keep the meta/progress file shapes simple but don't add anything that would make adding tiers later a breaking change (e.g. don't hardcode "one completion record per chapter" in a way that can't hold a tier number).

## 8. Language

- Always converse with the user in whatever language they're using right now, regardless of anything below.
- The *committed* document's language is controlled by `.onboard/config.json`'s `docLanguages` array, not by the live conversation language.
- If the conversation language isn't in `docLanguages`, still converse in it, but write the committed doc in the array's first entry instead. Never silently add a new language to the array on the user's behalf.
- If multiple languages are in the array, each gets an independent `docs/onboarding/<scope-slug>.<lang>.md` file, independently kept up to date via step 7 — don't try to keep them in lockstep or translate between them.

## 9. Committing

Never run `git commit` or `git push` on your own initiative. After generating or updating the artifacts in step 6, show the user what changed and explicitly ask whether to commit. Only run `git add`/`git commit` after they say yes. Treat `git push` as a separate, even more explicit ask — never bundle it into the same confirmation.
