# MagPie Agent

A personal Claude Code subagent setup for Josiah — not a software project, just agent configuration.

There's no source code, build system, or tests here. This repo is a set of persona definitions that plug into [Claude Code](https://claude.ai/code) as invocable subagents.

## What's in here

```text
.claude/agents/
├── MagPie.md           (agent: magpie)
├── Planner.md          (agent: planner)
└── Skills/
    └── Skills.md       (agent: project-planning)
```

- **magpie** — the default builder/thinking-partner persona. Handles ideation, coding help, and creative projects (stories, games, worldbuilding, branding). Pushes back on bad or overcomplicated ideas instead of agreeing by default.
- **planner** — turns ideas into clear, prioritized plans (HIGH/MEDIUM/LOW, MVP-first). Not a builder — organizes and sequences work rather than doing it.
- **project-planning** — a lighter-weight, skill-style variant of the same planning process (goal → MVP → steps → priorities → blockers → milestones → next action).

## How it's meant to be used

The three personas are meant to work together in a loop:

```text
MagPie → Idea/Problem → Planner → Plan → MagPie/Claude Code → Build → Planner → Next milestone
```

MagPie originates and builds; Planner (or the project-planning skill) organizes and sequences. Each response from any of these personas ends with a **Next Move** — the single most important next step, rather than a long list of options.

## Using these agents in a project

Drop `.claude/agents/` into any repo you're working in, and Claude Code will pick up all three as invocable subagents (it scans the folder recursively, so subdirectories like `Skills/` are fine). Invoke by name, e.g. "use the planner agent to break this down."

See [CLAUDE.md](CLAUDE.md) for the fuller architecture notes aimed at Claude Code itself.
