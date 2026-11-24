# Decision Log (`DECISION_LOG.md`)

Git tracks *what* changed. The log tracks *why*, and the human context behind it.

Create the log only after the user confirms. Then, after every completed fix, feature, revert, pivot, or scope change, append one entry:

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
- The log is personal working memory and normally stays out of version control (`.gitignore` it).
