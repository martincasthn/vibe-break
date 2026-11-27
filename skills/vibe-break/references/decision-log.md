# Decision Log (`DECISION_LOG.md`)

Git tracks *what* changed. The log tracks *why*, and the human context behind it.

Create the log automatically after the first completed change of a session, unless `AGENTS.md` or `CLAUDE.md` says `vibe-break: no-log` or `vibe-break: off`, or a log already exists (in `.vibe-break/`, the root, or `.agent/`).

- Write it to `.vibe-break/DECISION_LOG.md`, and create `.vibe-break/.gitignore` containing a single line: `*`. The folder ignores itself, so nothing tracked is edited and nothing inside `.git/` is touched (many agents block writes there).
- Tell the user in one line that it was started and how to turn it off.
- If hiding it fails for any reason, keep the log anyway and say what failed. Never skip the log because of that step.

Start the file with a `# Decision Log` heading. Then, after every completed fix, feature, revert, pivot, or scope change, append one entry:

- **Timestamp & Title:** `[YYYY-MM-DD HH:MM] - Short title`
- **Area / Tag:** feature or module name (used to spot repeated loops)
- **Change:** one sentence on what was modified or reverted
- **Why / Context:** Human context: what the user wanted, their mood or constraint, and which agent or source caused the change
- **Status:** written / verified / live
- **Open Questions / Next:** anything left unverified

Rules:

- Keep entries short. Newest at the bottom.
- Never write secrets (tokens, passwords, connection strings) into the log. Name the environment variable instead.
- When scope grows (new requirements, a client change, a spec revision), log it as its own entry and restate what is now in and out of scope.
- When reading the log, count entries per Area / Tag. Three or more unresolved entries on the same tag is a loop (see SKILL.md Section 3).
- The log is personal working memory and stays out of version control. Never commit it unless the user asks.
