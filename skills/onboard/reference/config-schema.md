# `.onboard/config.json` schema

Path: `.onboard/config.json` at the target repo root. Git-committed — these are team-level settings, not personal config.

```json
{
  "docLanguages": ["en"],
  "docsDirectory": "docs/onboarding",
  "commitByDefault": true
}
```

- `docLanguages`: array of language codes (e.g. `"en"`, `"ja"`), in priority order. The first entry is the fallback/default language for the committed doc when a contributor's conversation language isn't in this list.
  - One entry → effectively a single fixed doc language for the whole repo.
  - Multiple entries → one `<docsDirectory>/<scope-slug>.<lang>.md` file per entry, each independently kept up to date.
- `docsDirectory`: where all `/onboard` output files live (doc, corrections, meta, glossary), relative to the repo root. Defaults to `docs/onboarding`.
- `commitByDefault`: whether `/onboard` should default to offering a commit after generating/updating artifacts (`true`) or default to leaving changes uncommitted unless the user explicitly asks (`false`). Either way, `/onboard` always asks before committing (see `SKILL.md` step 9) — this only changes which answer is pre-selected/expected, never skips the ask itself.
- If this file doesn't exist when `/onboard` needs it, create it with defaults (`docLanguages` = the language of the current conversation, `docsDirectory` = `docs/onboarding`, `commitByDefault` = `true`). Don't ask the user to configure this upfront on first use — let it default naturally; a team that wants to change these runs `/setup-onboard` (or hand-edits the file) once.
- Never auto-append a new language to `docLanguages` just because someone happened to converse in it — that change should be an explicit, human-made edit (and then committed like any other config change).
