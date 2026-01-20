# Roadmap: GSD Quick Mode

## Overview

Quick mode adds a fast-path command (`/gsd:quick`) that executes small tasks with full GSD guarantees (atomic commits, STATE.md tracking) but skips optional verification agents. Quick tasks live in `.planning/quick/` separate from planned features.

## Features

### Core
- [x] **Quick Command** — Complete `/gsd:quick` command end-to-end

### Documentation
- [x] **Quick Mode Docs** — Update help.md, README.md, and GSD-STYLE.md

### UX
- [ ] **Native Question UI** — Use AskUserQuestion tool throughout all GSD commands

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

## Progress

| Feature | Status | Completed |
|---------|--------|-----------|
| Quick Command | Complete | 2026-01-19 |
| Quick Mode Docs | Complete | 2026-01-19 |
| Native Question UI | Pending | — |
