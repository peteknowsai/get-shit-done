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

### Parallel Workflow
- [ ] **gsd:discuss Command** — Discussion phase that outputs scope.md for features
- [ ] **gsd:run Command** — Background-capable research, planning, and execution
- [ ] **gsd:review Command** — Interactive walkthrough of completed changes

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

### gsd:discuss Command
**Goal:** Structured discussion phase that captures scope and requirements before execution
**Size:** medium
**Notes:**
- `gsd:discuss` with no args picks next feature from roadmap
- `gsd:discuss [feature]` discusses specific feature
- Interactive clarification of scope, requirements, decisions
- Outputs `.planning/features/{slug}/scope.md`
- All questions resolved before handoff to run phase

### gsd:run Command
**Goal:** Background-capable execution of research, planning, and implementation
**Size:** large
**Notes:**
- Reads scope.md from discuss phase
- Spawns background agent for heavy lifting
- Research codebase → write plan.md → execute → commit
- User can run `gsd:discuss` on another feature while this runs
- Writes summary.md when complete
- Uses Task tool with `run_in_background: true`

### gsd:review Command
**Goal:** Interactive walkthrough of completed feature changes
**Size:** medium
**Notes:**
- `gsd:review` reviews last completed feature
- `gsd:review [feature]` reviews specific feature
- Steps through changed files one by one
- Explains what changed and why
- Collects feedback: approve, request changes, or discuss

## Progress

| Feature | Status | Completed |
|---------|--------|-----------|
| Quick Command | Complete | 2026-01-19 |
| Quick Mode Docs | Complete | 2026-01-19 |
| Native Question UI | Complete | 2026-01-20 |
| gsd:discuss Command | Pending | — |
| gsd:run Command | Pending | — |
| gsd:review Command | Pending | — |
