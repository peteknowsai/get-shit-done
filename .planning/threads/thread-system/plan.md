# Thread: thread-system - Plan

**Created:** 2026-01-20

## Approach

Replace the monolithic `/gsd:next` command with a thread-based system that supports:
- Scoping multiple features into a single "thread"
- Background execution via sub-agents
- Parallel thread execution
- Interactive review before approval

## Tasks

### Feature: gsd:thread Command

#### Task 1.1: Create thread command
**Files:** commands/gsd/thread.md
**Details:** Interactive feature picker using AskUserQuestion, scope discussion, creates thread directory with scope.md
**Verify:** Command file exists with proper structure

### Feature: gsd:run Command

#### Task 2.1: Create run command
**Files:** commands/gsd/run.md
**Details:** Resolves thread, validates state, supports foreground/background execution modes, spawns gsd-thread-executor
**Verify:** Command file exists with proper structure

### Feature: gsd:review Command

#### Task 3.1: Create review command
**Files:** commands/gsd/review.md
**Details:** Interactive review of completed thread work, file walkthrough option, approval flow updates ROADMAP.md
**Verify:** Command file exists with proper structure

### Feature: gsd-thread-executor Agent

#### Task 4.1: Create thread executor agent
**Files:** agents/gsd-thread-executor.md
**Details:** Autonomous agent that reads scope.md, researches, creates plan.md, executes with atomic commits, writes summary.md
**Verify:** Agent file exists with proper structure

### Feature: Deprecate gsd:next

#### Task 5.1: Add deprecation notice
**Files:** commands/gsd/next.md
**Details:** Add deprecated section at top with migration guide to thread system
**Verify:** Deprecation notice present

#### Task 5.2: Update help command
**Files:** commands/gsd/help.md
**Details:** Replace /gsd:next with thread/run/review workflow, update file structure, update quick reference
**Verify:** Help reflects new workflow

### Feature: Update State Tracking

#### Task 6.1: Add Active Threads section to STATE.md
**Files:** .planning/STATE.md
**Details:** Add table tracking thread status (scoped, running, blocked, complete, approved)
**Verify:** Active Threads section present

## Dependencies

- AskUserQuestion tool (already available)
- Task tool for sub-agent spawning (already available)

## Risks

- Context rot during long execution: Mitigated by atomic commits and persistent state files
- Parallel thread conflicts: Mitigated by separate thread directories
