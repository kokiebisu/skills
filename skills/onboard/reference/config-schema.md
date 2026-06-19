# `.onboard/config.json` schema

Path: `.onboard/config.json` at the target repo root. Git-committed — this is a team-level setting, not personal config.

```json
{
  "docLanguages": ["en"]
}
```

- `docLanguages`: array of language codes (e.g. `"en"`, `"ja"`), in priority order. The first entry is the fallback/default language for the committed doc when a contributor's conversation language isn't in this list.
  - One entry → effectively a single fixed doc language for the whole repo.
  - Multiple entries → one `docs/onboarding/<scope-slug>.<lang>.md` file per entry, each independently kept up to date.
- If this file doesn't exist when `/onboard` needs it, create it with a single entry: the language of the current conversation. Don't ask the user to configure this upfront — let it default naturally on first use, since it's trivial to edit by hand afterward.
- Never auto-append a new language to this array just because someone happened to converse in it — that change should be an explicit, human-made edit (and then committed like any other config change).
