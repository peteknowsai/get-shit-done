---
name: gsd:next
description: Select next feature(s) to build, plan, and execute
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
  - TodoWrite
---

<objective>
The main work loop. Select what to build → discuss → research → plan → execute.

**Flow:**
1. Show progress
2. Present options (individual features or groups)
3. User selects
4. Discussion (capture vision)
5. Research (if needed)
6. Plan (native plan mode)
7. Execute (native todos)

**Repeat until project complete.**
</objective>

<context>
@.planning/ROADMAP.md
@.planning/STATE.md
</context>

<process>

## 1. Verify Project Exists

```bash
[ -f .planning/ROADMAP.md ] || { echo "No project found. Run /gsd:init first."; exit 1; }
```

## 2. Show Progress

Parse ROADMAP.md:
- Count completed features (marked with `[x]`)
- Count remaining features (marked with `[ ]`)
- Identify feature groups

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► NEXT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Progress: [████░░░░░░░░] 3/12 features

## Recently Completed
✓ User login
✓ Password reset
✓ Dashboard layout
```

## 3. Present Options

Build options from remaining features:

```
## What's Next?

1. Activity feed (medium)
2. All of Dashboard — 2 remaining features
3. OAuth integration (large)
4. Settings page (small)
```

Use AskUserQuestion:
```
question: "What do you want to work on?"
options:
  - "Activity feed" — Single feature, medium size
  - "All of Dashboard" — 2 features: Activity feed, Notifications
  - "OAuth integration" — Single feature, large size
  - "Settings page" — Single feature, small size
```

If user wants something not listed, discuss and potentially update roadmap.

## 4. Create Feature Directory

```bash
FEATURE_SLUG=$(echo "[feature-name]" | tr '[:upper:]' '[:lower:]' | tr ' ' '-')
mkdir -p ".planning/features/${FEATURE_SLUG}"
```

## 5. Discussion

Gather the user's vision for this feature.

Ask freeform: **"How do you imagine [feature] working?"**

Follow threads:
- What's the core behavior?
- Any specific UI/UX preferences?
- Edge cases they're thinking about?
- What does "done" look like?

Save insights to `.planning/features/{slug}/CONTEXT.md`:

```markdown
# [Feature Name] — Context

## Vision
[User's description of how it should work]

## Key Decisions
- [Decision 1]
- [Decision 2]

## Must-haves
- [Requirement 1]
- [Requirement 2]

## Out of scope
- [Exclusion 1]
```

## 6. Research Decision

Use AskUserQuestion:
```
question: "Research best practices for this feature?"
options:
  - "Yes, research" — Investigate standard approaches
  - "No, skip" — I know how to build this
```

**If "Yes":**

Spawn gsd-researcher agent:
```
Task(
  prompt="Research how to implement [feature]. Context: @.planning/features/{slug}/CONTEXT.md",
  subagent_type="gsd-researcher",
  description="Research [feature]"
)
```

Agent writes to `.planning/features/{slug}/RESEARCH.md`

Present key findings before continuing.

## 7. Enter Plan Mode

Use native Claude Code plan mode:

```
EnterPlanMode()
```

**In plan mode:**
1. Read CONTEXT.md and RESEARCH.md (if exists)
2. Explore relevant codebase areas
3. Design implementation approach
4. Write plan to `.planning/features/{slug}/PLAN.md`

**Plan format:**
```markdown
# [Feature Name] — Plan

## Approach
[High-level implementation strategy]

## Tasks

### 1. [Task Name]
**Files:** [files to create/modify]
**Details:** [specific implementation]
**Verify:** [how to confirm it works]

### 2. [Task Name]
...

## Success Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]
```

Exit plan mode when ready:
```
ExitPlanMode()
```

User approves the plan.

## 8. Execute

After plan approval, execute using native TodoWrite.

**Initialize todos from plan:**
```
TodoWrite([
  { content: "Task 1: [name]", status: "pending", activeForm: "Working on [name]" },
  { content: "Task 2: [name]", status: "pending", activeForm: "Working on [name]" },
  ...
])
```

**For each task:**
1. Mark as in_progress
2. Implement the task
3. Verify it works
4. Commit:
   ```bash
   git add [files]
   git commit -m "feat([feature]): [task description]"
   ```
5. Mark as completed
6. Move to next task

## 9. Complete Feature

After all tasks done:

**Update ROADMAP.md:**
- Mark feature as complete: `- [x] **[Feature Name]**`

**Update STATE.md:**
- Update current position
- Note any decisions made

**Commit:**
```bash
git add .planning/
git commit -m "docs: complete [feature-name]"
```

## 10. Offer Next

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► [FEATURE] COMPLETE ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Progress: [█████░░░░░░░] 4/12 features

Run /gsd:next to continue
```

**If all features complete:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► PROJECT COMPLETE 🎉
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All 12 features shipped!

What's next?
- /gsd:roadmap — Add more features
- Test everything manually
- Ship it!
```

</process>

<group_handling>
When user selects a feature group (e.g., "All of Dashboard"):

1. List features in the group
2. Plan each feature (can be one combined plan or separate)
3. Execute sequentially, committing after each
4. Mark all as complete when done

User can bail out between features if needed.
</group_handling>

<context_management>
**Fresh context is important.** If context is getting full:

1. Suggest `/clear` before starting a large feature
2. Research and planning can happen in subagents
3. Execution uses TodoWrite to track progress across clears

State persists in files:
- ROADMAP.md — what's done
- STATE.md — decisions and notes
- CONTEXT.md — feature vision
- PLAN.md — implementation plan
</context_management>

<success_criteria>
- [ ] Progress shown
- [ ] Options presented
- [ ] Feature selected
- [ ] Discussion captured in CONTEXT.md
- [ ] Research completed (if selected)
- [ ] Plan created via native plan mode
- [ ] Plan approved by user
- [ ] Tasks executed with native TodoWrite
- [ ] Each task committed
- [ ] Feature marked complete in ROADMAP.md
- [ ] User knows next steps
</success_criteria>
