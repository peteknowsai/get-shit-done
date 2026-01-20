# Thread: thread-system

**Created:** 2026-01-20
**Status:** scoped

## Features

- [ ] **gsd:thread Command** — Create and scope a feature thread
- [ ] **gsd:run Command** — Execute thread (background-capable)
- [ ] **gsd:review Command** — Review completed thread
- [ ] **Deprecate gsd:next** — Remove old monolithic command, thread system replaces it

## Scope

### gsd:thread Command
**Goal:** Interactive feature picker that creates scoped threads
**Approach:** Parse ROADMAP.md, present multi-select with AskUserQuestion, discuss scope, create scope.md
**Done when:** User can select features, name thread, discuss scope, and scope.md is created

### gsd:run Command
**Goal:** Execute threads with optional background mode
**Approach:** Spawn gsd-thread-executor agent, support foreground/background modes
**Done when:** Threads execute autonomously, user can run multiple in parallel

### gsd:review Command
**Goal:** Interactive review of completed thread work
**Approach:** Load thread artifacts, present summary, optional file walkthrough, collect approval
**Done when:** User can review changes, provide feedback, approve to mark features complete

### Deprecate gsd:next
**Goal:** Transition users to thread system
**Approach:** Add deprecation notice to next.md, keep functional for now
**Done when:** Deprecation notice added, help.md updated with new workflow

## Notes

Implementation completed in this session:
- Created commands/gsd/thread.md
- Created commands/gsd/run.md
- Created commands/gsd/review.md
- Created agents/gsd-thread-executor.md
- Updated commands/gsd/help.md
- Updated .planning/STATE.md with Active Threads section
- Added deprecation notice to commands/gsd/next.md
