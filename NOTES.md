# Notes

## CLAUDE.md

**What I put in:** a one-line description, the four commands I actually run
(`npm run dev`, `npm test`, single-test file, `npm run lint`), the conventions
that aren't obvious from a quick read (CommonJS is *required* because ESLint is
set to `sourceType: "script"`; all data access goes through `db/store.js`;
status codes to return), and an architecture paragraph explaining the
`require.main === module` trick that lets tests import `app` without opening a
port.

**What I left out:** the file tree and endpoint list (Claude can see `routes/`
in seconds), the course/submission steps from the README (one-off, not how the
code works), and anything from `.env` (nothing sensitive is committed anyway).
The goal was that every line saves a future session a lookup.

## Permission rules

```json
"allow": ["Bash(npm test:*)", "Bash(npm run lint:*)"]
"ask":   ["Bash(git push:*)"]
"deny":  ["Read(./.env)", "Bash(git push --force:*)"]
```

- **allow** — test and lint are safe, read-only, and run constantly; approving
  them every time is pure friction.
- **ask** — `git push` is fine to do, but I want to see it before it happens.
- **deny** — without `Read(./.env)`, Claude could pull real secrets into the
  context window (and from there into a transcript or a commit). Without
  `git push --force`, a well-meaning "clean up history" could overwrite a shared
  branch and lose other people's commits.
