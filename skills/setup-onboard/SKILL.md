---
name: setup-onboard
description: One-time setup for the `onboard` skill in a repo — configure doc languages, output directory, and default commit behavior. Run once per repo before relying on `/onboard`'s defaults, or whenever a team wants to change them.
---

Configure `.onboard/config.json` for this repo, per the `onboard` skill's `reference/config-schema.md` (read it first). This is optional — `/onboard` works fine with defaults if nobody ever runs this — but lets a team set things explicitly instead of inheriting whatever the first person who ran `/onboard` happened to get by default.

1. If `.onboard/config.json` already exists, show its current contents before asking anything, and treat the questions below as "change this?" rather than starting from a blank slate.
2. Ask, one at a time (per `grilling`'s one-question-at-a-time convention, since these are independent decisions a team should make deliberately, not rapid-fire):
   - **`docLanguages`**: which language(s) should the committed onboarding docs be written in? (array, priority order — see `reference/config-schema.md` for what multiple entries mean.)
   - **`docsDirectory`**: where should `/onboard`'s output files live? Default `docs/onboarding`.
   - **`commitByDefault`**: should `/onboard` default to offering a commit after generating docs (`true`), or default to leaving changes uncommitted unless explicitly asked (`false`)? Either way `/onboard` always asks before committing — this only changes the default answer it expects.
3. Write `.onboard/config.json` with the answers.
4. Show the user the file and ask whether to commit it now — same confirm-before-commit rule as `onboard`'s own step 9 (never commit without asking, regardless of what `commitByDefault` was just set to).
