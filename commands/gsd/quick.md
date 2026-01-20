---
name: gsd:quick
description: Execute ad-hoc tasks outside the roadmap
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - AskUserQuestion
  - TodoWrite
---

<objective>
Quick tasks that don't belong on the roadmap.

**Use for:**
- Bug fixes
- Config changes
- One-off adjustments
- Small improvements
- "Just do this real quick"

**Not for:** Planned features (use `/gsd:next`)

**Same guarantees:**
- Atomic commits
- State tracking
- But no research, no plan mode overhead
</objective>

<context>
@.planning/STATE.md
</context>

<process>

## 1. Get Task

Ask freeform: **"What do you want to do?"**

If task sounds like a feature (substantial new functionality), use AskUserQuestion:

```
question: "This sounds like a roadmap feature. Add to roadmap instead?"
options:
  - "Yes, add to roadmap" — Use /gsd:roadmap to plan properly
  - "No, just do it quick" — It's small, handle it now
```

If "Yes" → redirect to /gsd:roadmap
If "No" → continue with quick task

## 2. Create Quick Directory

```bash
mkdir -p .planning/quick

# Find next number
last=$(ls -1d .planning/quick/[0-9][0-9][0-9]-* 2>/dev/null | sort -r | head -1 | xargs -I{} basename {} | grep -oE '^[0-9]+')
next_num=$([ -z "$last" ] && echo "001" || printf "%03d" $((10#$last + 1)))

# Create slug and directory
slug=$(echo "$DESCRIPTION" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9]/-/g' | sed 's/--*/-/g' | cut -c1-40)
QUICK_DIR=".planning/quick/${next_num}-${slug}"
mkdir -p "$QUICK_DIR"
```

## 3. Plan Inline

Break the task into steps. Keep it simple.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► QUICK #[NNN]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Task: [description]

Steps:
1. [step 1]
2. [step 2]
3. [step 3]
```

Use AskUserQuestion to confirm before executing:

```
question: "Ready to execute these steps?"
options:
  - "Execute" — Let's do it
  - "Adjust" — I want to change something
```

If "Adjust" → discuss changes, update steps, re-confirm
If "Execute" → proceed

## 4. Execute with TodoWrite

```
TodoWrite([
  { content: "Step 1", status: "in_progress", activeForm: "Working on step 1" },
  { content: "Step 2", status: "pending", activeForm: "Working on step 2" },
  ...
])
```

For each step:
1. Do the work
2. Commit:
   ```bash
   git add [files]
   git commit -m "fix: [what was done]"
   ```
3. Mark complete, move to next

## 5. Complete

Write summary to `${QUICK_DIR}/SUMMARY.md`:

```markdown
# Quick Task #[NNN]: [Description]

**Date:** [date]
**Commits:** [hashes]

## What was done
- [change 1]
- [change 2]
```

Update STATE.md "Last activity":
```
Last activity: [date] — Quick task #[NNN]: [description]
```

Commit:
```bash
git add .planning/quick/ .planning/STATE.md
git commit -m "docs: quick task #[NNN] complete"
```

## 6. Done

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► QUICK #[NNN] DONE ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[description]

Ready for more:
- /gsd:quick — another quick task
- /gsd:next — back to roadmap
```

</process>

<success_criteria>
- [ ] Task understood
- [ ] Quick directory created
- [ ] Work completed
- [ ] Commits made
- [ ] Summary written
- [ ] STATE.md updated
</success_criteria>
