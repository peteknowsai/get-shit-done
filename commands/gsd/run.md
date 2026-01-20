---
name: gsd:run
description: Execute thread (background-capable)
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
---

<objective>
Execute a scoped thread by spawning the gsd-thread-executor agent.

**Flow:**
1. Resolve which thread to run (from arg or pick)
2. Validate thread state
3. Ask execution mode (background or foreground)
4. Update STATE.md
5. Spawn gsd-thread-executor
6. Handle result

**Supports:** Background execution for parallel threads.
</objective>

<context>
@.planning/STATE.md
</context>

<process>

## 1. Verify Project Exists

```bash
[ -f .planning/ROADMAP.md ] || { echo "No project found. Run /gsd:init first."; exit 1; }
```

## 2. Resolve Thread

**If argument provided:** Use that as thread slug.

**If no argument:**

```bash
# Find scoped threads (have scope.md, no summary.md)
for dir in .planning/threads/*/; do
  slug=$(basename "$dir")
  [ -f "$dir/scope.md" ] && [ ! -f "$dir/summary.md" ] && echo "$slug"
done
```

**If no scoped threads:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No scoped threads found.

Run /gsd:thread to scope a new thread.
```
Exit.

**If one scoped thread:** Use it.

**If multiple scoped threads:**

Use AskUserQuestion:
```
question: "Which thread to run?"
header: "Thread"
multiSelect: false
options:
  - label: "{thread-1}"
    description: "{feature count} features — {status}"
  - label: "{thread-2}"
    description: "{feature count} features — {status}"
  - label: "{thread-3}"
    description: "{feature count} features — {status}"
```

## 3. Validate Thread State

```bash
THREAD_DIR=".planning/threads/${SLUG}"
```

**Check scope.md exists:**
```bash
[ -f "$THREAD_DIR/scope.md" ] || { echo "Thread missing scope.md. Run /gsd:thread first."; exit 1; }
```

**Check not already complete:**
```bash
[ -f "$THREAD_DIR/summary.md" ] && { echo "Thread already complete. Run /gsd:review $SLUG to review."; exit 1; }
```

**Check not already running:**
```bash
if [ -f "$THREAD_DIR/progress.md" ]; then
  status=$(grep -o 'Status: [a-z]*' "$THREAD_DIR/progress.md" | head -1 | cut -d' ' -f2)
  if [ "$status" = "executing" ] || [ "$status" = "researching" ] || [ "$status" = "planning" ]; then
    echo "Thread already running."
    exit 1
  fi
fi
```

**Check for blocked state:**
```bash
if [ -f "$THREAD_DIR/blocked.md" ]; then
  echo "Thread is blocked. See blocked.md for details."
  # Show blocking issue
  cat "$THREAD_DIR/blocked.md"
  exit 1
fi
```

## 4. Show Thread Info

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN: {slug}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread: {name}
Features: {count}
{list features from scope.md}
```

## 5. Ask Execution Mode

Use AskUserQuestion:
```
question: "How should this thread run?"
header: "Mode"
multiSelect: false
options:
  - label: "Background (Recommended)"
    description: "Continue working while it runs"
  - label: "Foreground"
    description: "Watch execution in real-time"
```

## 6. Update STATE.md

Update "Active Threads" table:
- Set status to "running"
- Update "Started" date if first run

## 7. Spawn Executor

**Background mode:**

```
Task(
  prompt: "Execute thread. Scope: @{THREAD_DIR}/scope.md",
  subagent_type: "gsd-thread-executor",
  description: "Execute {slug}",
  run_in_background: true
)
```

Show:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN: {slug} — STARTED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread running in background.

You can continue working:
- /gsd:thread — Scope another thread
- /gsd:quick — Quick task
- Check progress: read .planning/threads/{slug}/progress.md

When complete, review with:
- /gsd:review {slug}
```

**Foreground mode:**

```
Task(
  prompt: "Execute thread. Scope: @{THREAD_DIR}/scope.md",
  subagent_type: "gsd-thread-executor",
  description: "Execute {slug}",
  run_in_background: false
)
```

Wait for completion, then handle result.

## 8. Handle Result

**If THREAD COMPLETE:**

Update STATE.md:
- Set status to "complete"
- Set "Completed" date

Show:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN: {slug} — COMPLETE ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread complete!
{summary from executor}

Next:
- /gsd:review {slug} — Review and approve changes
```

**If CHECKPOINT REACHED:**

Update STATE.md:
- Set status to "blocked"

Show:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN: {slug} — BLOCKED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread blocked.
{blocking issue from executor}

After resolving:
- /gsd:run {slug} — Resume execution
```

**If THREAD FAILED:**

Update STATE.md:
- Set status to "failed"

Show:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► RUN: {slug} — FAILED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread failed.
{error from executor}

Options:
- Fix manually and run /gsd:run {slug}
- /gsd:thread — Start fresh with new scope
```

</process>

<resume_behavior>

## Resuming Blocked Threads

When a blocked thread is run again:

1. Check for blocked.md
2. If user indicates block resolved, delete blocked.md
3. Spawn executor with resume context

The executor reads progress.md to know where to continue.

## Resuming After Context Clear

If context clears mid-execution:
1. Thread state is in progress.md
2. Running `/gsd:run {slug}` detects running state
3. Offers to check status or restart

</resume_behavior>

<parallel_threads>

## Running Multiple Threads

Background execution enables parallel threads:

```
/gsd:thread                    # Scope thread A
/gsd:run thread-a --background # Start A
/gsd:thread                    # Scope thread B
/gsd:run thread-b --background # Start B
# Both running simultaneously
```

**STATE.md tracks all threads:**

```markdown
## Active Threads

| Thread | Status | Features | Started | Completed |
|--------|--------|----------|---------|-----------|
| thread-a | running | 2 | 2026-01-20 | — |
| thread-b | running | 3 | 2026-01-20 | — |
```

**Check on threads:**
```bash
# See all thread statuses
ls .planning/threads/*/progress.md 2>/dev/null | while read f; do
  slug=$(dirname "$f" | xargs basename)
  status=$(grep "Status:" "$f" | head -1)
  echo "$slug: $status"
done
```

</parallel_threads>

<success_criteria>
- [ ] Project verified
- [ ] Thread resolved (from arg or picker)
- [ ] Thread state validated
- [ ] Execution mode selected
- [ ] STATE.md updated
- [ ] gsd-thread-executor spawned
- [ ] Result handled appropriately
- [ ] Next steps shown
</success_criteria>
