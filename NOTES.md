# NOTES

## What went into CLAUDE.md, and what I left out

I kept four parts: a one-line description, the four npm commands (plus how to run a single test file), six conventions, and a short architecture map. Every convention comes from something I read in the code: CommonJS from `.eslintrc.json` (`sourceType: "script"`), the `db/store.js` data layer, the `{ error }` response shape in `routes/users.js`, the `node:test` + `supertest` pattern in `tests/users.test.js`, and the `require.main === module` guard in `server.js`. Claude can follow each one without asking a question.

I deliberately left out anything Claude can see by reading the code (the list of endpoints, the seed users, what each route returns), anything that would go stale quickly, and any secrets or `.env` values. The file stays short so that every line is actually read.

## Permission rules

- **allow** `npm test` and `npm run lint`: safe, read-only checks I want Claude to run on its own after every change.
- **ask** `git push`: pushing leaves my machine, so I want to confirm each time.
- **deny** `Read(./.env)`: the real secrets live there.
- **deny** `git push --force` and `git push -f`: a force-push can overwrite shared history and cannot be undone easily.

Without the `.env` deny rule, Claude could read real secrets (for example a database URL) into the conversation, where they could end up in logs or be sent on to other tools. Without the force-push deny rule, one mistaken command could destroy teammates' commits.

## Something I found along the way

The repo's `.gitignore` has leading spaces in front of `.env`, `npm-debug.log*` and `.claude/settings.local.json`. Git treats those spaces as part of the pattern, so the rules do not match. I tested this: a `.env` file shows up as untracked (`git check-ignore` found no rule for it). `.claude/settings.local.json` was only ignored because of my global git ignore, not the repo's. I fixed it by removing the leading spaces, and re-tested: `git check-ignore -v .env` now matches `.gitignore:2`. The `.env` deny rule is still worth keeping, as a second layer on top of the ignore rule.

## Verification

Done by hand in a fresh Claude Code session in this folder:

- [x] `/memory` shows `CLAUDE.md` as loaded
- [x] `/permissions` shows the allow / ask / deny rules above
- [x] Asking "How do I run the tests here?" is answered from `CLAUDE.md`
