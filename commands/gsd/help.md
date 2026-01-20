---
name: gsd:help
description: Show GSD commands
---

<objective>
Display the GSD command reference.

Output ONLY the reference content. No project analysis, no status, no suggestions.
</objective>

<reference>
# GSD — Get Shit Done

## Commands

| Command | Purpose |
|---------|---------|
| `/gsd:init` | Initialize a new project |
| `/gsd:next` | Select feature → plan → build |
| `/gsd:roadmap` | Review and realign roadmap |
| `/gsd:quick` | Ad-hoc tasks outside roadmap |
| `/gsd:debug` | Systematic debugging |
| `/gsd:help` | Show this reference |

## Workflow

```
/gsd:init           ← once, at start

/gsd:next           ← main loop
/gsd:next           ← repeat until done
/gsd:next

/gsd:roadmap        ← when you need to rethink priorities

/gsd:next           ← back to building
```

## Files

```
.planning/
├── ROADMAP.md          ← features (grouped, ordered)
├── STATE.md            ← project memory
├── config.json         ← settings
├── research/           ← project research
├── features/           ← per-feature context, research, plans
│   └── {feature}/
│       ├── CONTEXT.md
│       ├── RESEARCH.md
│       └── PLAN.md
├── quick/              ← quick tasks
└── debug/              ← debug sessions
```

## Quick Reference

**Starting a project:**
```
/gsd:init
```

**Building features:**
```
/gsd:next
```

**Quick fixes:**
```
/gsd:quick
```

**Debugging:**
```
/gsd:debug "something is broken"
```

**Rethinking priorities:**
```
/gsd:roadmap
```
</reference>
