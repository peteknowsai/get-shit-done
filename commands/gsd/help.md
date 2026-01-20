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
| `/gsd:thread` | Scope a feature thread |
| `/gsd:run` | Execute thread (background-capable) |
| `/gsd:review` | Review completed thread |
| `/gsd:roadmap` | Review and realign roadmap |
| `/gsd:quick` | Ad-hoc tasks outside roadmap |
| `/gsd:debug` | Systematic debugging |
| `/gsd:help` | Show this reference |

## Workflow

```
/gsd:init           ← once, at start

/gsd:thread         ← scope features to build
/gsd:run            ← execute (can run in background)
/gsd:review         ← approve completed work

/gsd:thread         ← scope next thread
/gsd:run --bg       ← run in background, continue working
/gsd:thread         ← scope another thread (parallel!)

/gsd:roadmap        ← when you need to rethink priorities
```

## Files

```
.planning/
├── ROADMAP.md          ← features (grouped, ordered)
├── STATE.md            ← project memory
├── config.json         ← settings
├── research/           ← project research
├── threads/            ← thread workspaces
│   └── {thread}/
│       ├── scope.md    ← from /gsd:thread
│       ├── plan.md     ← from executor
│       └── summary.md  ← completion summary
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
/gsd:thread                  # scope features
/gsd:run                     # execute
/gsd:review                  # approve
```

**Parallel execution:**
```
/gsd:run --background        # run in background
/gsd:thread                  # scope another while it runs
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
