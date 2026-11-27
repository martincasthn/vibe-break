# Changelog

## v1.3.1

- **Fix: the decision log was never created.** Since v1.0.0, the log was only offered at the end of the first-run audit, and that offer was skipped whenever the first message was already a task. In practice, no test project ever got a log. Now the agent creates `.vibe-break/DECISION_LOG.md` automatically after the first finished change and tells you in one line. A `.vibe-break/.gitignore` containing `*` keeps the folder out of git, with no edits to your `.gitignore` and no writes inside `.git/`. If hiding the log ever fails, the log is still created. Existing logs at the root or in `.agent/` are still used.
- **Opt out per project:** `vibe-break: no-log` in `AGENTS.md` or `CLAUDE.md`.
- **Task-first sessions:** when the first message is already a task, the audit shrinks to a two- or three-line status and the agent does the task.
- **Explicit deep onboarding:** if you ask for a full read, the agent reads deeper but says up front that it will use more context.
- **Tested before release, in real headless Claude Code sessions (`claude -p`, Sonnet)** on a throwaway project with a real bug:
  - A first attempt wrote to `.git/info/exclude`. Claude Code blocks writes there as a sensitive location, so the agent skipped the log entirely. That led to the self-ignoring `.vibe-break/` folder and the "never skip the log" rule.
  - Session 1 ("npm test fails, the cart total is wrong. Fix it.") fixed the bug, verified it with `npm test`, created `.vibe-break/DECISION_LOG.md` with a correct entry, and told the user. `git status` showed only `cart.js`.
  - Session 2 ("Add a test for an empty cart.") found the existing log and appended a second entry. No second log was created.

## v1.3.0

- **Audit budget:** the first-run audit skims instead of reading. Top-level listing (skipping build and cache folders), manifests and main docs only, with no source files. It never searches outside the project, never re-reads a file, and loads the skill once per session. Prompted by a real onboarding that used 14% of a 1M context: about 6% was fixed tool overhead, and most of the rest came from reading whole source files, a repeated read and a whole-drive search.
- **Scout delegation:** on large projects, the scan goes to a cheaper-model subagent that returns a summary citing file paths. The main agent spot-checks the claims that matter, and a scout summary alone never justifies an edit or a "verified" state, since small or older models summarize loosely.
- **Model roles:** scout (cheap) reads, builder (mid-tier) edits, thinker (top model) plans and decides.
- **Skills & tools line:** also flags installed plugins or MCP servers that are irrelevant to the project, since each one costs context in every session.
- **README:** a new "Keeping context cheap" section, including how to keep a plugin out of one project in Claude Code.

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
