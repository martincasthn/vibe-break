# Skill Patterns Research (November 2026)

Notes from reading well-known Agent Skills to learn *design patterns*, not to copy content. All credit to the original authors; see sources below.

**Guiding constraint for vibe-break:** stay short and act like a companion. Keep the core well under 500 lines (it is about 140 today), and add a pattern only when a test shows it is needed.

## Skills read

| Skill | Author | Notable for |
|---|---|---|
| `writing-skills` | obra / Superpowers | Treats skill writing like test-driven development: baseline run without the skill, record failures, write the skill, re-test |
| `using-superpowers` | obra / Superpowers | Mandatory "Using X to Y" announcement, red-flag table, user instructions outrank skills |
| `grill-me` + README | Matt Pocock | Split between user-invoked orchestrator skills and model-invoked building blocks (`disable-model-invocation: true`) |
| `skill-creator` | Anthropic | Evals with/without the skill, "pushy" trigger descriptions, description tuning with near-miss prompts, "explain why instead of ALWAYS/NEVER" |
| `cold-email` | Corey Haines / marketingskills | Reads a shared context file before asking, "don't block on missing inputs", an AI-tells blacklist and a pre-send quality check |

Not read: the "reverse engineer anything" skills (several community skills share that name, none clearly the original).

## Ten patterns

| # | Pattern | vibe-break today |
|---|---|---|
| 1 | Test skills like code: baseline run without the skill, then with it, repeated | Partly: v1.3.1 was tested with the skill in `claude -p`, once, no baseline |
| 2 | Rationalization tables: "what the agent tells itself" vs "reality" | Missing |
| 3 | Red-flag lists: moments that mean "stop and apply the skill" | Partly (breakdown signals, loop counts) |
| 4 | Trigger-first descriptions ("Use when…"), pushy enough to fire | Mixed: description opens with what it is, triggers come late |
| 5 | Description tuning with ~20 test prompts, mostly near-misses | Missing |
| 6 | Progressive disclosure with a size budget and "read when…" pointers | Done (`references/`, Section 9) |
| 7 | Explain the why; avoid shouting (authors disagree on caps) | Mostly done |
| 8 | User-run orchestrators vs model-run building blocks | Relevant for future helpers |
| 9 | Read shared context before asking; don't block on missing inputs | Done (audit, log, task-first fix) |
| 10 | Pre-ship checklist plus an "AI tells" blacklist | Idea for a "before you say done" check |

Also: a visible announcement when a skill fires ("Using X to Y"). It would have exposed the never-created log much earlier.

## Candidates for v1.4.0 (test-first)

1. **Baseline tests:** 3–4 scenarios (cart bug, "I'm done, just fix it", pasted AI plan, destructive request), each run with and without vibe-break in `claude -p`. The results decide what else is worth adding.
2. **Rationalization tables:** only for rules the baseline shows agents breaking.
3. **Visible announcement:** a short "vibe-break: full mode" line so users know it loaded.

## To look at later

- **Graphify:** suggested for memory and graph patterns for quick reference. Not researched yet.
- Revisit after real-project testing of v1.3.1, with concrete problems in hand.

## Sources

- obra/superpowers: https://skills.sh/obra/superpowers and https://skills.sh/obra/superpowers/using-superpowers
- Matt Pocock's skills: https://raw.githubusercontent.com/mattpocock/skills/main/README.md and https://claude.com/plugins/mattpocock-skills
- Anthropic skill-creator: https://raw.githubusercontent.com/anthropics/skills/main/skills/skill-creator/SKILL.md
- Corey Haines cold-email: https://tessl.io/registry/skills/github/coreyhaines31/marketingskills/cold-email
- Reverse-engineer skills (not read): https://claudskills.com/skills/reverse-engineer-anything/
- Lists: https://github.com/heilcheng/awesome-agent-skills, https://github.com/louisblythe/Sales-Skills
