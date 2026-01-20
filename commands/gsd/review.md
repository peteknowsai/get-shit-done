---
name: gsd:review
description: Review completed thread
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
---

<objective>
Interactive review of a completed thread's changes.

**Flow:**
1. Resolve which thread to review (from arg or pick)
2. Load thread artifacts (scope.md, plan.md, summary.md)
3. Get git history for thread commits
4. Present summary
5. Optional: Walk through changed files
6. Collect feedback
7. If approved: Mark features complete in ROADMAP.md
8. If on feature branch: Offer to merge to main

**Goal:** User understands and approves the work, then merges.
</objective>

<critical>
**MANDATORY: Use AskUserQuestion tool for ALL user interactions.**

Do NOT have freeform conversation. Every question = AskUserQuestion tool call.
</critical>

<context>
@.planning/STATE.md
@.planning/ROADMAP.md
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
# Find completed threads (have summary.md)
for dir in .planning/threads/*/; do
  slug=$(basename "$dir")
  [ -f "$dir/summary.md" ] && echo "$slug"
done
```

**If no completed threads:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► REVIEW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No completed threads to review.

Check thread status:
- /gsd:run — See running threads
- /gsd:thread — Scope a new thread
```
Exit.

**If one completed thread:** Use it.

**If multiple completed threads:**

Use AskUserQuestion:
```
question: "Which thread to review?"
header: "Thread"
multiSelect: false
options:
  - label: "{thread-1}"
    description: "Completed {date} — {feature count} features"
  - label: "{thread-2}"
    description: "Completed {date} — {feature count} features"
```

## 3. Load Thread Artifacts

```bash
THREAD_DIR=".planning/threads/${SLUG}"
```

Read:
- `$THREAD_DIR/scope.md` — Original scope
- `$THREAD_DIR/plan.md` — Execution plan
- `$THREAD_DIR/summary.md` — Completion summary

**Validate thread is complete:**
```bash
[ -f "$THREAD_DIR/summary.md" ] || { echo "Thread not complete yet. Check progress with /gsd:run $SLUG"; exit 1; }
```

## 4. Get Git History

Extract commits for this thread:

```bash
# Get commits mentioning this thread
git log --oneline --all --grep="thread: ${SLUG}" | head -20
```

Or use commit hashes from summary.md.

Get list of changed files:

```bash
# Get all files changed across thread commits
git diff --name-only {first_commit}^..{last_commit}
```

## 5. Present Summary

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► REVIEW: {slug}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Thread Summary

**Completed:** {date}
**Features:** {count}
**Commits:** {count}
**Files Changed:** {count}

## Features Delivered

{from summary.md - list of features with descriptions}

## Commits

{commit hash} — {message}
{commit hash} — {message}
...

## Files Changed

**New:** {count}
{list new files}

**Modified:** {count}
{list modified files}
```

## 6. Review Options

Use AskUserQuestion:
```
question: "How would you like to review?"
header: "Review"
multiSelect: false
options:
  - label: "Walk through files"
    description: "See each change with explanation"
  - label: "Quick summary"
    description: "Just show the highlights"
  - label: "Skip to approval"
    description: "I trust the changes"
```

## 7. File Walkthrough (if selected)

**For each changed file:**

Show the file or diff:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 FILE: {path}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{For new files: show content}
{For modified files: show diff or key changes}

**Why:** {explanation of what this file does and why it changed}
```

Use AskUserQuestion:
```
question: "This file looks..."
header: "Feedback"
multiSelect: false
options:
  - label: "Good"
    description: "Continue to next file"
  - label: "Needs changes"
    description: "I want to modify this"
  - label: "Skip"
    description: "Don't care about this file"
```

**If "Needs changes":**
- Ask what changes are needed
- Make the changes (Edit tool)
- Show updated file
- Commit as fixup:
  ```bash
  git add {file}
  git commit -m "fix({feature}): {description}

  Review feedback on thread: {slug}

  Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
  ```
- Continue to next file

**After all files reviewed:**

Continue to approval.

## 8. Quick Summary (if selected)

Show condensed view:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 QUICK SUMMARY: {slug}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## What Was Built

{Feature 1}:
- {key point 1}
- {key point 2}

{Feature 2}:
- {key point 1}
- {key point 2}

## Key Files

- {most important file}: {what it does}
- {second important file}: {what it does}

## Testing Notes
{how to verify it works}
```

Continue to approval.

## 9. Final Approval

Use AskUserQuestion:
```
question: "Approve this thread?"
header: "Approve"
multiSelect: false
options:
  - label: "Approve"
    description: "Mark features complete in roadmap"
  - label: "Request changes"
    description: "Need more modifications"
  - label: "Reject"
    description: "Start over with this feature"
```

**If "Approve":**

1. Mark features complete in ROADMAP.md:
   - For each feature in scope.md
   - Change `- [ ]` to `- [x]`

2. Update STATE.md:
   - Update "Active Threads" status to "approved"
   - Update progress count

3. Commit:
   ```bash
   git add .planning/ROADMAP.md .planning/STATE.md
   git commit -m "docs: approve thread {slug}

   Features completed:
   $(list features)

   Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
   ```

4. Check for feature branch and offer merge:

   ```bash
   CURRENT_BRANCH=$(git branch --show-current)
   MAIN_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo "main")
   ```

   **If on feature branch (not main/master/dev):**

   Use AskUserQuestion:
   ```
   question: "Merge {branch} to {main}?"
   header: "Merge"
   multiSelect: false
   options:
     - label: "Merge and push (Recommended)"
       description: "Merge to {main}, push both branches"
     - label: "Merge only"
       description: "Merge to {main}, don't push"
     - label: "Skip merge"
       description: "Stay on feature branch"
   ```

   **If "Merge and push" or "Merge only":**

   ```bash
   # Merge to main
   git checkout {main}
   git merge {feature_branch} --no-ff -m "Merge {feature_branch}: {thread_name}

   Features:
   $(list features)

   Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
   ```

   **If "Merge and push":**

   ```bash
   git push origin {main}
   git push origin {feature_branch}
   ```

   **If merge conflicts:**
   - Show conflict files
   - Ask user how to proceed (resolve or abort)

5. Show completion:
   ```
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    GSD ► REVIEW: {slug} — APPROVED ✓
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

   Thread approved! Features marked complete in roadmap.
   {If merged: "Merged {feature_branch} → {main}"}
   {If pushed: "Pushed to origin"}

   Progress: [████████░░░░] {n}/{total} features

   Next:
   - /gsd:thread — Start next feature
   - /gsd:roadmap — Review priorities
   ```

**If "Request changes":**

Ask what changes are needed (freeform).

Make changes, commit as review feedback.

Return to approval question.

**If "Reject":**

Use AskUserQuestion:
```
question: "What should happen to this thread?"
header: "Action"
multiSelect: false
options:
  - label: "Archive"
    description: "Keep files but don't mark complete"
  - label: "Delete"
    description: "Remove thread directory"
  - label: "Rescope"
    description: "Start over with new scope"
```

Handle accordingly.

</process>

<review_best_practices>

## Effective Reviews

**Focus on:**
- Does it do what scope.md said?
- Are there obvious bugs?
- Does it follow existing patterns?
- Is it tested appropriately?

**Don't focus on:**
- Nitpicking style (unless egregious)
- Hypothetical improvements
- Scope creep ("we should also add...")

## Quick Feedback Loop

For small issues:
1. Note the issue
2. Fix immediately
3. Commit as review feedback
4. Continue

Don't block approval for minor issues that can be fixed during review.

</review_best_practices>

<success_criteria>
- [ ] Project verified
- [ ] Thread resolved (from arg or picker)
- [ ] Thread artifacts loaded
- [ ] Git history retrieved
- [ ] Summary presented
- [ ] Review mode selected and executed
- [ ] Feedback collected
- [ ] If approved: ROADMAP.md updated
- [ ] If approved: STATE.md updated
- [ ] Changes committed
- [ ] If on feature branch: Merge offered
- [ ] If merge selected: Branch merged to main
- [ ] If push selected: Changes pushed to origin
</success_criteria>
