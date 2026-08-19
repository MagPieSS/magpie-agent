# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is not a software project — there is no source code, build system, package manifest, or test suite here. The repository contains only Claude Code subagent configuration:

```text
.claude/agents/
├── MagPie.md           (agent: magpie)
├── Planner.md          (agent: planner)
└── Skills/
    └── Skills.md       (agent: project-planning)
```

Claude Code scans `.claude/agents/` recursively, so a file's subdirectory location doesn't affect registration — only its frontmatter `name` field does. (These files have previously been moved out of `.claude/agents/` by accident, which silently unregisters them as subagents even though their content is untouched — if a persona seems to have stopped working, check its location first.)

All three files use standard YAML frontmatter (`name`, `description` between `---` delimiters), so all three register as invocable subagents. A file may add optional fields on top of that pair — `model`, `tools`, `effort` — to override the inherited model, restrict available tools, or set reasoning effort for that persona specifically. Unknown/malformed keys (duplicates, wrong tool-name casing) won't necessarily error loudly, so double-check frontmatter by eye when a persona's behavior doesn't match its file. Concretely: [agents/Planner.md](agents/Planner.md) currently sets `model: GPT-4.1`, which is not a valid Claude Code model identifier (valid values are names like `opus`, `sonnet`, `haiku`, or `fable`) — this is a likely misconfiguration, not an intentional cross-provider setup, and probably falls back to a default model silently rather than erroring.

`Skills/Skills.md` is a naming trap: despite the folder name, it is not a Claude Code Skill. Real Skills live under `.claude/skills/<name>/SKILL.md` and are invoked via the Skill tool; this repo has no such directory. Because `Skills/Skills.md` sits under `.claude/agents/` with standard `name`/`description` frontmatter, it registers exactly like the other two files — as a subagent (`project-planning`), invoked the same way (Agent tool / by name), not as a lighter-weight in-context flow.

This repo is also meant to be portable: per [README.md](../README.md), the usual workflow is to copy the whole `.claude/agents/` folder into another project's repo so that project picks up all three personas. Treat this directory itself as the source of truth for the persona definitions, not as a place that consumes them.

Because there is nothing to build, lint, or test, do not assume standard dev commands exist. If the user asks you to run/build/test "the project," clarify what they actually want — they likely mean a different, unrelated project they intend to work on inside this directory, since it is currently just an agent-config scaffold named after "MagPie."

## Architecture: three cooperating personas

These files define personas/subagents for a personal assistant system, not application code. They are meant to be read together as one design:

- **MagPie** ([agents/MagPie.md](agents/MagPie.md)) — the default builder/thinking-partner persona for the user (Josiah). Handles ideation, coding help, and creative projects. Pushes back on bad ideas rather than agreeing by default, and explicitly resists scope creep ("That's a good Phase 2 idea. Let's finish the first version first.").
- **planner** ([agents/Planner.md](agents/Planner.md)) — a subagent focused purely on turning ideas into prioritized, phased plans (HIGH/MEDIUM/LOW priority, MVP-first). Explicitly *not* a builder.
- **project-planning** ([agents/Skills/Skills.md](agents/Skills/Skills.md)) — a variant of the same planning process (goal → MVP → steps → priorities → blockers → milestones → next action). Despite the "Skills" folder name and the file's own description of itself as a "skill," it registers and is invoked identically to the other two — a regular subagent, not a Claude Code Skill.

The intended workflow, per [agents/Planner.md](agents/Planner.md), is a loop:

```text
MagPie → Idea/Problem → Planner → Plan → MagPie/Claude Code → Build → Planner → Next milestone
```

MagPie originates and builds; Planner (or the project-planning subagent) organizes and sequences. When acting as one of these personas, do not duplicate the other's role — MagPie should not produce formal phased plans, and Planner/project-planning should not write code or make build decisions.

## Shared conventions across all three personas

- End substantive responses with a **Next Move** (or **Next Action**) section naming the single most important next step — this is a consistent, deliberate pattern across all three files, not a one-off.
- Keep plans/scope small by default: avoid inventing tasks, phases, or architecture beyond what's needed for the smallest useful (MVP) version. Do not default to a 5-phase project structure for small projects.
- Prefer direct, plain language over corporate/assistant-speak; avoid unnecessary questions or over-explaining when enough information is already available to make a call.
