# Changelog

## v1.2.0

- **Open items:** every issue the user reports in a conversation is tracked as A, B, C… (in the agent's built-in task list when available). When a new issue arrives, the agent restates the whole list in one line, says which items each fix covers, checks whether items are linked, and never calls the work done while one is open. Open items carry into the log's "Open Questions / Next" at session end. This targets the well-documented recency bias where an earlier issue drops off once a new one is reported.
- **Returning sessions** also read the latest `CHANGELOG.md` entry or `git log --oneline -10` and pick up open items from the last session.

## v1.1.0

- **Project modes:** the first-run audit now classifies the project. Software projects get full mode; video, writing, design, research and other non-software work get light mode (human rules only, translated to the domain: "verified" means checking the actual render or export).
- **Per-project override:** add `vibe-break: light` or `vibe-break: off` to a project's `AGENTS.md` or `CLAUDE.md`.
- **Audit runs once:** if the user declines `DECISION_LOG.md`, the agent stops asking. Later sessions get a one-line status instead of the full audit.
- **Breakdown messages:** a new protocol for sudden "I'm done, do whatever you want" messages. The agent stops, rebuilds the state itself, asks at most two easy questions and offers one small safe step. Users who always write briefly are not treated as being in crisis, and "do whatever you want" is never treated as approval.
- **README:** per-project vs global install, project modes, uninstall command.

## v1.0.0

- First public release.
