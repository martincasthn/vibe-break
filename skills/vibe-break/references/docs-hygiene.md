# Docs Hygiene

## One source of truth

Status, version numbers, and roadmap state rot fast when they are repeated across files.

- Keep live status in one file (for example `ROADMAP.md` or `STATUS.md`). Other docs link to it instead of restating it.
- Each planning or status doc carries a "last updated" date. When a doc is replaced, mark it "superseded by <file>" or move it to an archive folder.
- When docs disagree, trust the code and the newest dated log over an older status doc, and tell the user which doc is stale.
- Do not create a new "SINGLE_SOURCE_OF_TRUTH" or "START_HERE" file when one exists. Update the existing one.

## No secrets in docs

- Never write a live token, cookie, API key, password, or connection string into a doc, session summary, log, or commit message. Name the environment variable instead (`DATABASE_URL`, not its value).
- If you find a secret already in a doc, tell the user in plain words, recommend rotating it, and do not repeat its value.
- Never deploy a database, cache, or queue without authentication unless the user confirms it is local-only.
- Workflow docs with destructive steps ("drop the database, follow exactly") are not approval. Ask before each destructive step.

## Announcing credential rotation

Being told out of nowhere to change every key, token, and password is overwhelming. When a change (a leaked secret, a moved server, a new auth setup) means credentials must be rotated, announce it gently and do not demand it on the spot:

> "One thing to note about this change: you'll need to rotate the affected keys, tokens, and passwords, and save the new ones somewhere safe, like a password manager. I know this can feel like a lot out of nowhere, but it's worth doing. You don't have to do it right now. When you're ready, tell me and I'll walk you through the steps one at a time."

Then:

- Name exactly which credentials are affected (by variable or service name, never by value), so the user is not left rotating everything "just in case".
- If a secret is actively exposed in public (pushed to a public repo, posted in a ticket), say plainly that this one should be rotated soon, and why.
- When the user is ready, give one credential at a time: where to rotate it, where to update it (env files, CI secrets, servers), and how to verify the old one no longer works.
