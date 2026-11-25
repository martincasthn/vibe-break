# vibe-break

**An Agent Skill that keeps AI coding sessions grounded.** It stops hallucination cascades, blocks blind copy-pastes between agents, locks the agent onto your real tech stack, routes library questions to live docs, and calls a timeout when you're stuck in a debugging loop.

Works with any [Agent Skills](https://agentskills.io)-compatible tool: Claude Code, Cursor, OpenCode, Codex and others.

## Install

**This project only** (run inside the project folder):

```bash
npx skills add martincasthn/vibe-break
```

**All your projects** (global, user-level):

```bash
npx skills add martincasthn/vibe-break -g
```

Short form: `npx skills i martincasthn/vibe-break`. To uninstall: `npx skills remove vibe-break` (add `-g` for a global install).

### Project modes

A global install doesn't force software rules onto every project. On the first run the agent picks a mode:

- **Full mode:** software projects (apps, services, scripts, infrastructure). Every rule applies.
- **Light mode:** video, writing, design, research and other non-software work. Only the human rules apply: no fluff, loop and burnout care, the copy-paste guard, verify before "done" (for example, actually checking the render), options mean stop, and clean git.

To override it for one project, add a line to that project's `AGENTS.md` or `CLAUDE.md`:

```text
vibe-break: light
vibe-break: off
```

**Manual install:** copy `skills/vibe-break/` into your agent's skills folder.

| Tool | Global path |
|------|-------------|
| Claude Code | `~/.claude/skills/vibe-break/` |
| Cursor | `~/.cursor/skills/vibe-break/` |
| Shared (`.agents`) | `~/.agents/skills/vibe-break/` |

For a single project, use `.claude/skills/vibe-break/` (or your tool's equivalent) inside the repo instead.

## The problem → the fix

| Problem | What vibe-break does |
|---------|----------------------|
| **Hallucination cascades.** One agent invents an API, the next builds on it, and three files later nothing runs. | Checks every pasted path, function and API against the actual codebase before using it. |
| **Blind cross-agent copy-paste.** You paste a wall of output from another AI and the agent applies it as-is. | Stops with *"No — let's pause on this paste,"* flags conflicts with your stack, and asks what you were actually trying to do. |
| **Stack drift.** You ask for a fix in your Python app and get a React + TypeScript rewrite. | Reads your manifests and `AGENTS.md`, then only suggests tools the project already uses unless you ask. |
| **Stale library syntax.** The agent writes code against an API that changed two versions ago. | Pulls current docs first (Context7 or another docs skill) and tells you in one line which check ran. |
| **Lost "why".** Git shows what changed, not why you reverted the auth refactor at 2 a.m. | Keeps a short `DECISION_LOG.md` with the human context behind each change. |
| **Debugging loops and burnout.** The same bug gets "fixed" five times in an hour. | After 3 attempts it stops coding and asks diagnostic questions. After 4+, it suggests a break, a fresh chat, or one tiny isolated step. |
| **"I'm fried, but I can't stop."** It's 9 a.m. and your shift ends at 5 p.m., so "take a break" doesn't help. | A Recovery Protocol: restate the goal, check git history, check the docs, audit any pasted output, verify against live docs and the web, then take one small verified step. |
| **"I'm done. Do whatever you want."** A frustrated one-liner that an agent reads as permission to rewrite everything. | It stops, rebuilds the state itself from git and the log (what changed, what was tested, what failed), asks at most two yes/no questions, and offers one small safe step. Users who always write briefly are not treated as being in crisis. |
| **Not knowing what's already installed.** Useful skills and tools sit unused, and every scan burns the expensive model. | The first-run audit lists the skills and tools that matter, suggests at most two, and suggests cheaper subagents for read-only work. |
| **"Done!" that was never checked.** The agent says "production ready" while core flows still fail. | Every change is reported as *written*, *verified* (with the real check output) or *live*. Exit code 0 isn't proof, and the agent checks before asking you to test. |
| **Agents acting before you decide.** It lists three options, then applies option 1. It force-pushes or drops a database as a "routine step." | Options mean stop. Destructive steps (data, git history, `.env`, volumes) need your explicit yes, after a plain-language list of what will be lost. |
| **Status docs that contradict each other.** Five files list different versions, and nobody knows which one is current. | One live status file, dated docs with "superseded by" notes, and code plus the newest log win over stale docs. |
| **Secrets leaking into notes.** Tokens and passwords get pasted into session summaries. | Docs name the environment variable, never the value. Secrets it finds are flagged for rotation. |
| **Debugging a server that isn't running.** Stale caches, old IPs, the wrong build. | Checks the process, host, build, cache and env file first, and hits the endpoint itself. |
| **Big-bang sessions.** Six objectives at once, and a middleware file that was never wired in. | One change, one check, then the next. New modules must actually be imported and used. Handoffs say what's done, half-done and untouched. |
| **DevOps scripts that "succeed" but didn't.** A Paramiko command exit code nobody read, a CRLF bash script that dies with `bad interpreter`, a missing `rsync` slash, a password hard-coded into `deploy.ps1`. | Real exit codes are checked (`recv_exit_status()`, `$LASTEXITCODE`, `set -euo pipefail`), line endings are fixed per script type, and keys are referenced by path. If a prompt blocks the agent, it writes a local script that reads secrets from environment variables, never values. |
| **Surprise "rotate everything."** One change suddenly means new keys, tokens and passwords everywhere. | The agent names exactly which credentials are affected, says it doesn't have to happen right now, and walks you through one credential at a time when you're ready. |
| **Windows encoding accidents.** A shell rewrite breaks UTF-8, or a 4,000-line phantom line-ending diff appears. | Edits go through the editor tool, diff size is checked, and `.gitattributes` / `.editorconfig` are suggested. |
| **AI co-authors in your history.** Agents add `Co-Authored-By` trailers and "Generated with" footers to your commits. | Keeps git metadata clean: no AI trailers or footers, and no changes to your `user.name` / `user.email` unless you ask. |
| **Filler and fake apologies.** "Certainly! I'm so sorry for the confusion…" | No fluff. It confirms a 2–3 bullet plan before writing code. |

## How it works

1. **First run:** no `DECISION_LOG.md` yet, so the agent audits the workspace and reports a 5-line status: project type and mode (with the detected stack), docs/PRD, structure, useful skills and tools, and what you want to do. It creates `AGENTS.md` and `DECISION_LOG.md` only after you confirm. If you decline, it doesn't ask again, and later sessions get a one-line status instead of the full audit.
2. **Returning sessions:** the agent reads `AGENTS.md` and the last 5–10 log entries to restore context and spot loops.
3. **During work:** the copy-paste guard, docs routing and loop detection run as needed. Detailed checklists in `references/` (environment check, remote commands and scripts, docs hygiene, slices and handoff, Windows edits, log format) load only when the situation calls for them, which keeps the always-loaded core small and cheap on tokens.
4. **After each change:** the agent appends a short log entry. A real one looks like this:

```markdown
- [2026-11-24 15:40] - Fix invoice PDF totals
  - Area / Tag: billing-pdf
  - Change: Rounded line totals before summing instead of after.
  - Why / Context: Third attempt today; user tired and on a deadline. The earlier fix came from another agent's paste that assumed a different currency library.
  - Status: verified (unit tests 18/18), not deployed
  - Open Questions / Next: Confirm rounding rule with accounting.
```

Three unresolved entries with the same tag tell the agent it's in a loop.

Trivial edits (a typo, a rename, formatting) skip the plan-and-confirm step, so the skill stays out of your way on small things. To turn it off for the rest of a session, say "vibe-break off".

## Auto-trigger (optional)

To load the skill at the start of every session, add a line like this to your global `AGENTS.md`, `CLAUDE.md` or rules file:

> At the start of every project session, and whenever I paste large output from another AI agent, repeat a fix 3+ times, seem frustrated, or suddenly send short "I'm done, just fix it" messages, load and follow the `vibe-break` skill (it picks full or light mode by project type).

## Privacy

`DECISION_LOG.md` is personal working memory. This repo's `.gitignore` excludes it. Add the same lines to your own projects if you don't want to commit it:

```gitignore
DECISION_LOG.md
.agent/DECISION_LOG.md
```

## About me (and why this exists)

I'm not an AI engineer, and I wouldn't call myself an expert coder. I spent about nine years in print design and production, then taught myself automation and software from 2022 on. Today I build automations, internal tools and web apps from Honduras.

I didn't build vibe-break from theory. Over many projects I fell, many times: agents that said "done" when nothing worked, fixes pasted from one AI into another, the same bug "solved" five times in an afternoon, and billions of tokens burned across multiple agents. Along the way I kept private notes of every changelog, failure and late night. This skill is those notes turned into rules, so the next person (and the next agent) doesn't have to learn them the hard way.

I hope you find it useful. Issues and pull requests are welcome, especially stories of where it helped or got in your way.

**Martín Castellanos** · [martincast.site](https://martincast.site/) · [LinkedIn](https://www.linkedin.com/in/martin-castellanos-a1bb58144/)

## License

[MIT](LICENSE) © Martín Castellanos
