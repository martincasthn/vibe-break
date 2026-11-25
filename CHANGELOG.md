# Changelog

## v1.1.0

- **Project modes:** the first-run audit now classifies the project. Software projects get full mode; video, writing, design, research and other non-software work get light mode (human rules only, translated to the domain: "verified" means checking the actual render or export).
- **Per-project override:** add `vibe-break: light` or `vibe-break: off` to a project's `AGENTS.md` or `CLAUDE.md`.
- **Audit runs once:** if the user declines `DECISION_LOG.md`, the agent stops asking. Later sessions get a one-line status instead of the full audit.
- **Breakdown messages:** a new protocol for sudden "I'm done, do whatever you want" messages. The agent stops, rebuilds the state itself, asks at most two easy questions and offers one small safe step. Users who always write briefly are not treated as being in crisis, and "do whatever you want" is never treated as approval.
- **README:** per-project vs global install, project modes, uninstall command.

## v1.0.0

- First public release.
