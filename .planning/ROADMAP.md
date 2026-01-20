# Roadmap: GSD Quick Mode

## Overview

Quick mode adds a fast-path command (`/gsd:quick`) that executes small tasks with full GSD guarantees (atomic commits, STATE.md tracking) but skips optional verification agents. Quick tasks live in `.planning/quick/` separate from planned features.

## Features

### Core
- [x] **Quick Command** — Complete `/gsd:quick` command end-to-end

### Documentation
- [x] **Quick Mode Docs** — Update help.md, README.md, and GSD-STYLE.md

### UX
- [x] **Native Question UI** — Use AskUserQuestion tool throughout all GSD commands

### Thread System
- [ ] **gsd:thread Command** — Create and scope a feature thread
- [ ] **gsd:run Command** — Execute thread (background-capable)
- [ ] **gsd:review Command** — Review completed thread

## Feature Details

### Quick Command
**Goal:** User can run `/gsd:quick` (with interactive prompt) and have it execute with full state tracking
**Size:** medium
**Notes:** Command file with pre-flight validation, directory setup, orchestration (planner spawn, executor spawn, state update)

### Quick Mode Docs
**Goal:** Quick mode is documented in all relevant locations
**Size:** small
**Notes:** help.md lists command, README.md explains when to use it, GSD-STYLE.md documents patterns

### Native Question UI
**Goal:** All GSD commands use Claude Code's native AskUserQuestion tool instead of text prompts
**Size:** medium
**Notes:**
- Replace freeform "What's on your mind?" with structured options where applicable
- Update /gsd:init, /gsd:next, /gsd:roadmap, /gsd:quick, /gsd:debug
- Provides selectable options in CLI, better UX
- Users can still type custom responses via "Other" option

### gsd:thread Command
**Goal:** Create a thread that groups one or more roadmap features
**Size:** medium
**Notes:**
- `gsd:thread` shows pending features, user picks which to include
- `gsd:thread [name]` creates named thread, then pick features
- Thread = grouping of related features to build together
- Interactive clarification of scope, requirements, decisions
- Outputs `.planning/threads/{slug}/scope.md` covering all selected features
- All questions resolved before handoff to run phase
- Completing thread marks included features as done in ROADMAP.md

### gsd:run Command
**Goal:** Execute a thread with background-capable agents
**Size:** large
**Notes:**
- Reads scope.md from thread directory (may include multiple features)
- Spawns background agent for heavy lifting (P-thread pattern)
- Research codebase → write plan.md → execute → commit
- User can run `gsd:thread` to scope another thread while this runs
- Writes summary.md when complete
- Uses Task tool with `run_in_background: true`
- Enables parallel threads (Boris Cherny's 5+ agents pattern)

### gsd:review Command
**Goal:** Review a completed thread with interactive walkthrough
**Size:** medium
**Notes:**
- `gsd:review` reviews last completed thread
- `gsd:review [name]` reviews specific thread
- Steps through changed files one by one
- Explains what changed and why
- Collects feedback: approve, request changes, or iterate

## Progress

| Feature | Status | Completed |
|---------|--------|-----------|
| Quick Command | Complete | 2026-01-19 |
| Quick Mode Docs | Complete | 2026-01-19 |
| Native Question UI | Complete | 2026-01-20 |
| gsd:thread Command | Pending | — |
| gsd:run Command | Pending | — |
| gsd:review Command | Pending | — |
