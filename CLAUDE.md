# CLAUDE.md

Starter Express API (users + health) used as the base for the Claude Code course projects. Data is in memory and resets on restart.

## Commands

- `npm install` — install dependencies (run once after cloning)
- `npm run dev` — start the API with auto-reload on http://localhost:3000
- `npm test` — run all tests (`node --test`); one file: `node --test tests/users.test.js`
- `npm run lint` — ESLint over the whole repo

CI (`.github/workflows/ci.yml`) runs `npm run lint` then `npm test` on Node 22. Both must pass before you finish a change.

## Conventions

- CommonJS only: use `require` / `module.exports`, not `import` / `export` (ESLint is set to `sourceType: "script"`).
- Route files never touch data directly; they call functions exported by `db/store.js`.
- Validate input inside the route handler and answer errors as `res.status(<code>).json({ error: "<message>" })` — 400 for bad input, 404 for a missing resource.
- Tests use `node:test`, `node:assert` and `supertest`, and import `app` from `../server`. Never call `app.listen` in tests.
- Keep `app.listen` inside the `require.main === module` guard in `server.js`.
- Read config from `process.env` (see `.env.example`); never hardcode secrets and never read or edit `.env`.

## Architecture

- `server.js` — entry point; builds the Express `app`, mounts routers, exports `app`.
- `routes/` — one file per resource (`users.js`, `health.js`), each exporting an `express.Router`. Add new resources as a new file and mount it in `server.js`.
- `db/store.js` — the only data layer: in-memory arrays plus helper functions.
- `tests/` — one `*.test.js` file per resource.
