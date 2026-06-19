# Shared glossary structure

Path: `docs/onboarding/glossary.<lang>.md`. One file per configured language, shared across **all** scopes in the repo (not per-scope) — the point is that a term established while touring one scope is recognized and reused when touring a different scope later, so the team builds one common vocabulary instead of each scope reinventing its own.

```markdown
# Glossary

## <Term>

<Default definition + the established analogy/diagram used for it, if any.>

**First introduced in:** <scope-slug> / "<chapter title>"

<If the term means something different in another scope, add a per-scope override instead of overwriting the default:>

### In <scope-slug>

<This scope's distinct definition.>

**First introduced in:** <scope-slug> / "<chapter title>"
```

Rules:
- One `##` heading per term, alphabetical or introduction order — pick one and stay consistent within a file.
- When reusing a term in a later chapter, reuse the glossary's existing analogy/wording rather than inventing a new one — that consistency is the entire point of this file.
- Never silently overwrite another scope's definition of the same term. If a new scope's usage conflicts with the existing entry, add a `### In <scope-slug>` sub-section rather than replacing the top-level definition — the conflict itself is worth surfacing to the learner (it usually means the org uses the same word for two different things).
- Only add a term here if it recurs across chapters or is genuinely non-obvious — not every noun mentioned in a chapter needs a glossary entry.
