# Decision Log (`DECISION_LOG.md`)

Git tracks *what* changed. The log tracks *why*, and the human context behind it.

## Creating it

**Never lose history.** Before creating the log, read the exact path `.vibe-break/DECISION_LOG.md`; create it only if that read reports the file does not exist. Never decide from a directory listing (a plain `ls` hides dot-folders). Before creating anything, check whether `.vibe-break/DECISION_LOG.md` exists (hidden folder: use file tools, `ls -a` in bash, or `Get-ChildItem -Force` in PowerShell) and check the root and `.agent/`. A failed listing command is not proof the log is missing. If a log exists, append to it with your editor tool; never overwrite it, never recreate it, never write it with `>`, a full-file write, or PowerShell redirection (Windows PowerShell 5.1 `>>` writes UTF-16). The only time entries move is archiving (below), and then they are copied first.

Create the log automatically after the first completed change of a session, unless `AGENTS.md` or `CLAUDE.md` says `vibe-break: no-log` or `vibe-break: off`, or a log already exists (in `.vibe-break/`, the root, or `.agent/`).

- Write it to `.vibe-break/DECISION_LOG.md`, and create `.vibe-break/.gitignore` containing a single line: `*`. The folder ignores itself, so nothing tracked is edited and nothing inside `.git/` is touched (many agents block writes there).
- Tell the user in one line that it was started and how to turn it off.
- If hiding it fails for any reason, keep the log anyway and say what failed. Never skip the log because of that step.

## Template (copy exactly)

```markdown
# Decision Log

## Current state
- **Key decisions:** <the 3–5 choices that shape the project, each a few words>
- **Project rules:** <standing rules the user set, e.g. "no commit without OK">
- **Open items:** <unfinished work or questions, newest first>
- **Updated:** YYYY-MM-DD HH:MM

## [YYYY-MM-DD HH:MM] - Short title
- **Area / Tag:** <one or two lowercase tags, reused across entries, e.g. auth, naming, cart>
- **Change:** <one sentence: what was modified or reverted>
- **Why / Context:** <what the user wanted, their mood or constraint, which agent or source caused it>
- **Status:** <written | verified (check + result) | live>
- **Open Questions / Next:** <anything left unverified, or "none">
```

Entry rules:

- Use the real local time (`date` or the system clock), not a guess. Every entry has date **and** time.
- One entry per meaningful decision or finished change, not per small edit. Six lines at most.
- Link to project docs (`docs/DESIGN_DECISIONS.md`, a PR, a commit hash) instead of repeating their content.
- Reuse existing tags so entries can be found later. Check the tags already in the file before inventing a new one.
- Never write secrets (tokens, passwords, connection strings). Name the environment variable instead.
- When scope grows (new requirements, a client change, a spec revision), log it as its own entry and restate what is in and out of scope.
- After appending, update the "Current state" block if a key decision, rule, or open item changed.

## Upgrading an older log

Logs created before v1.4.0 have no `## Current state` block and may use a looser entry format. On the first append:

1. Insert a `## Current state` block (the four bullets above) directly below `# Decision Log`, filled from the existing entries: lasting decisions, standing user rules, and the latest open items.
2. Do not reformat, reorder, or rewrite older entries. Insert and append only.
3. Append the new entry in the current format.

## Reading it

- **Session start:** read only the "Current state" block and the last 5 entries. Never read the whole file by default.
- **"Why did we decide X?", confusion, or frustration about past choices:** search instead of reading everything. Grep the log (and `.vibe-break/archive/`) for keywords and tags, open the matching entry plus the 2–3 entries before and after it, and answer with the entry's date and title, e.g. "On 2026-11-26 23:00 (Name chosen), we picked it because…"
- **Loops:** count unresolved entries per tag. Three or more on the same tag is a loop (see SKILL.md Section 3).

## Keeping it small

When the file passes about 150 lines, offer once per session: "The decision log is N lines. Want me to archive older entries? Nothing gets deleted." When the user says yes (or asks to archive at any time):

1. Copy every entry except the last 10 to `.vibe-break/archive/DECISION_LOG-YYYY-MM.md` (append if that file exists; keep entries unchanged).
2. Check the copy: the archive must contain every moved entry heading. Only then remove those entries from the live log.
3. Refresh the "Current state" block so nothing important lives only in the archive.
4. Tell the user in one line: "Archived older log entries to `.vibe-break/archive/`; nothing was deleted."

The log is personal working memory and stays out of version control. Never commit it unless the user asks.
