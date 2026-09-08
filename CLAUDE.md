# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express API serving `/users` and `/health` endpoints, backed by an in-memory store. It is the practice codebase for the Claude Code course; each course level builds on it.

## Commands

- `npm run dev` — start the API with auto-reload (`node --watch`) on http://localhost:3000
- `npm test` — run the full suite with Node's built-in test runner (`node --test`)
- `node --test tests/users.test.js` — run a single test file
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint then test on every push and PR

## Conventions

- CommonJS only (`require` / `module.exports`) — `.eslintrc.json` sets `sourceType: "script"`, so `import`/`export` will not parse.
- One router file per resource in `routes/`, mounted in `server.js` with `app.use("/resource", ...)`.
- Routes never touch the users array directly — all reads and writes go through `db/store.js`.
- Validate in the route handler: `400` for bad input, `404` for a missing record, `201` on create.
- `req` / `res` / `next` are exempt from `no-unused-vars`; other unused vars are a lint warning.

## Architecture

`server.js` is the entry point: creates the app, installs `express.json()`, mounts the route files, and calls `app.listen` **only when run directly** (`require.main === module`). It always exports `app` so tests can import it with `supertest` and no port is opened.

`db/store.js` is a module-level in-memory array with `getAllUsers` / `getUserById` / `createUser` and an internal `nextId` counter. Data resets on every restart — there is no persistence layer.

Tests live in `tests/` and hit the exported `app` directly via `supertest`; there is no running server during tests.
