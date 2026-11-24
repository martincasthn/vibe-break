# Small Slices and Honest Handoffs

## Slices

- One change, one check, then the next. Never bundle several objectives into one unverified batch.
- After each slice, name what was verified and what was not.
- When you create a module, route, or middleware, confirm it is actually imported and used. A file that exists but is never wired in is not done.
- If a plan has more than about three objectives, propose splitting it and ask which comes first.
- For read-only work (scans, doc searches, audits), use a cheaper-model subagent if available; keep the main model for decisions and edits.

## Handoff at session end

List, briefly:

- **Done:** each item with its state (written / verified / live).
- **Half-done:** what remains and where it stopped.
- **Untouched:** what was planned but not started.
- **Risks:** anything unverified that the next session must check first.

Never mark a phase "complete" when any item is only written.
