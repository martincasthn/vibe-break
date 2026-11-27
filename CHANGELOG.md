# Changelog

## v1.4.0

- **Fix (important): an existing log could be overwritten.** In v1.3.1, an agent that didn't look inside the hidden `.vibe-break/` folder could decide there was no log and replace it with a new one, losing all history. Now the agent lists `.vibe-break/` first, uses the log it finds, and only ever appends to it, never with `>` or a full-file write.
- **Visible status line:** the first reply of a session starts with `vibe-break: full mode · log: …`, so you can see the skill is active.
- **Loads once:** if a rule already loaded the skill, it isn't invoked again.
- **Fixed log template:** a `## Current state` block (key decisions, project rules, open items, updated) followed by consistent entries, with the time taken from the system clock. The template is now in the core, because agents rarely opened the reference file.
- **Ask about the past:** "why did we decide X?" is answered from the log, citing the entry's date.
- **Windows-safe log handling:** the log is found with file tools or the right command for the shell (`ls -a` in bash, `Get-ChildItem -Force` in PowerShell), and a failed listing is never taken as proof the log is missing. Writes go through the editor tool, never shell redirection, because `>>` in Windows PowerShell 5.1 writes UTF-16 and mangles the file.
- **Archive on request:** say "archive the decision log" and older entries move to `.vibe-break/archive/DECISION_LOG-YYYY-MM.md`. The last 10 stay, every entry is checked before removal, and nothing is deleted.

### Tested before release (real `claude -p` sessions, Sonnet, throwaway projects)

Three rounds, with fixes between them:

- **Fresh project** ("npm test fails… fix it"): status line, skill loaded once, bug fixed and verified, log created with the exact template and the real time, one-line notice. The first round had no status line and an invented log format; the second round labelled a code project "light mode". Both are fixed in the final round.
- **Existing 217-line log** ("why did we choose SQLite? also fix the cart test"): the **first round overwrote the whole log**, which is the bug above. In the final round the agent appended with `>>` (all 30 earlier entries kept), answered the SQLite question from the 2026-10-12 entry, tracked both requests as open items, and respected the log's "no commit without OK" rule.
- **Archive on request** ("Yes, archive it."): 21 entries moved to the archive and 10 kept, 31 of 31 preserved, live log down from 224 to 77 lines, and the Current state block kept the key decisions.

- **Sanity pass against v1.3.1:** the original v1.3.1 scenario (fix the cart, then a second session adds an empty-cart test) still passes: status line in both sessions, one log, the first entry kept, the second appended, plain UTF-8.

**Known, not a regression:** in a writing-only project with no code, a one-off edit request didn't load vibe-break at all, so there was no status line and no log. v1.3.1 behaves the same way in the identical test, so this predates v1.4.0. It's on the list for a future release.

**Not working yet:** agents still read the whole log instead of just the Current state block and recent entries, and they didn't offer to archive on their own once the log passed 150 lines. Both rules stay in the skill as guidance but aren't promised; archiving works when you ask for it.

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
