---
name: gsd:thread
description: Create and scope feature threads
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
Create a thread that groups one or more roadmap features for execution.

**Flow:**
1. Parse ROADMAP.md for pending features
2. Present feature picker (multi-select with AskUserQuestion)
3. Name the thread
4. Discuss scope for selected features
5. Create thread directory and scope.md
6. Update STATE.md
7. Commit and show next steps

**Output:** `.planning/threads/{slug}/scope.md`
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

## 2. Check for Existing Threads

```bash
# Check for scoped but not yet run threads
ls .planning/threads/*/scope.md 2>/dev/null | while read f; do
  dir=$(dirname "$f")
  [ ! -f "$dir/summary.md" ] && echo "$dir"
done
```

**If scoped threads exist, present choice:**

Use AskUserQuestion:
```
question: "You have scoped threads. What would you like to do?"
header: "Action"
multiSelect: false
options:
  - "Run existing" — Execute a scoped thread with /gsd:run
  - "New thread" — Scope a new thread
```

If "Run existing" → tell user to run `/gsd:run` and exit.

## 3. Parse ROADMAP.md for Pending Features

Extract pending features:
- Look for `- [ ]` lines (unchecked checkboxes)
- Group by `### ` section headers
- Extract feature name (bold text after checkbox)
- Extract description (text after em-dash if present)

**Feature structure:**
```
{
  section: "Thread System",
  name: "gsd:thread Command",
  description: "Create and scope a feature thread"
}
```

**If no pending features:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► THREAD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All features are complete!

Next:
- /gsd:roadmap — Add more features
```
Exit.

## 4. Feature Picker

Present features using AskUserQuestion with multi-select.

**Grouping strategy:**
- If a section has 2+ features, offer "All of {Section}" as an option
- Always offer individual features
- Maximum 4 options per question (AskUserQuestion limit)
- If more than 4 options needed, paginate

**First question - select features:**

```
question: "Select features for this thread"
header: "Features"
multiSelect: true
options:
  - label: "All of {Section} ({count})"
    description: "{feature1}, {feature2}, ..."
  - label: "{Feature Name}"
    description: "{feature description}"
  - label: "{Feature Name}"
    description: "{feature description}"
  - label: "{Feature Name}"
    description: "{feature description}"
```

**If user selects "Other":** Ask freeform what they want to build.

**If more features than fit in 4 options:**

After first selection, ask:
```
question: "Add more features to this thread?"
header: "More"
multiSelect: false
options:
  - label: "Done selecting"
    description: "Continue with {N} selected features"
  - label: "Add more"
    description: "Show remaining features"
```

If "Add more" → show next batch of features, repeat until "Done selecting".

## 5. Name the Thread

Suggest a name based on selected features:
- If single feature: use feature name as slug
- If multiple related features: use section name
- If mixed: use first feature or ask user

Use AskUserQuestion:
```
question: "Name this thread?"
header: "Name"
multiSelect: false
options:
  - label: "{suggested-name}"
    description: "Based on selected features"
  - label: "{section-name}"
    description: "Group name"
```

If "Other" → user provides custom name.

Generate slug: lowercase, hyphens, max 30 chars.

## 6. Create Thread Directory

```bash
THREAD_DIR=".planning/threads/${SLUG}"
mkdir -p "$THREAD_DIR"
```

## 7. Scope Discussion

For each selected feature, discuss scope:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► THREAD: {name}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Let's scope each feature in this thread.
```

**For each feature:**

Ask freeform: **"How do you imagine {feature} working?"**

Follow threads:
- What's the core behavior?
- Any specific UI/UX preferences?
- Edge cases?
- What does "done" look like?

Capture key points for scope.md.

**After all features discussed:**

Use AskUserQuestion:
```
question: "Ready to create this thread?"
header: "Confirm"
multiSelect: false
options:
  - label: "Create thread"
    description: "Save scope and continue"
  - label: "Discuss more"
    description: "Continue refining scope"
```

If "Discuss more" → continue freeform discussion.

## 8. Write scope.md

Write to `${THREAD_DIR}/scope.md`:

```markdown
# Thread: {name}

**Created:** {ISO date}
**Status:** scoped

## Features

- [ ] **{Feature 1}** — {description}
- [ ] **{Feature 2}** — {description}

## Scope

### {Feature 1}
**Goal:** {from discussion}
**Approach:** {from discussion}
**Done when:** {acceptance criteria}

### {Feature 2}
**Goal:** {from discussion}
**Approach:** {from discussion}
**Done when:** {acceptance criteria}

## Notes
{any additional context from discussion}
```

## 9. Update STATE.md

Add or update "Active Threads" section in STATE.md:

```markdown
## Active Threads

| Thread | Status | Features | Started | Completed |
|--------|--------|----------|---------|-----------|
| {slug} | scoped | {count} | {date} | — |
```

**Status values:** scoped, running, blocked, complete

## 10. Commit

```bash
git add .planning/threads/${SLUG}/scope.md .planning/STATE.md
git commit -m "thread({slug}): scope {count} features

Features:
$(for f in "${FEATURES[@]}"; do echo "- $f"; done)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

## 11. Show Next Steps

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► THREAD: {name} ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Thread scoped with {count} features:
{list features}

Next:
- /gsd:run {slug} — Execute this thread
- /gsd:run {slug} --background — Run in background
- /gsd:thread — Scope another thread
```

</process>

<feature_parsing>

## ROADMAP.md Format

```markdown
### Section Name
- [ ] **Feature Name** — Description
- [x] **Completed Feature** — Description
```

## Parsing Rules

1. `### ` starts a new section
2. `- [ ]` is a pending feature
3. `- [x]` is a completed feature (skip)
4. `**text**` is the feature name
5. ` — ` (em-dash with spaces) separates name from description
6. If no em-dash, description is empty

## Example Parse

Input:
```markdown
### Thread System
- [ ] **gsd:thread Command** — Create and scope a feature thread
- [ ] **gsd:run Command** — Execute thread (background-capable)
- [x] **Native Question UI** — Complete
```

Output:
```
[
  { section: "Thread System", name: "gsd:thread Command", description: "Create and scope a feature thread" },
  { section: "Thread System", name: "gsd:run Command", description: "Execute thread (background-capable)" }
]
```

</feature_parsing>

<pagination_strategy>

## When >4 Features

AskUserQuestion supports max 4 options. Handle overflow:

**Strategy 1: Group by section**
- Show "All of {Section}" for sections with 2+ features
- Individual features fit in remaining slots

**Strategy 2: Paginate**
- Show first 3 features + "Show more..."
- "Show more..." reveals next batch
- Continue until user says "Done selecting"

**Strategy 3: Smart grouping**
- Analyze feature names for patterns
- Group related features automatically
- "All database features (3)" → db-schema, db-migrations, db-seeding

**Preferred:** Strategy 1 (group by section) with Strategy 2 fallback.

</pagination_strategy>

<success_criteria>
- [ ] Project verified
- [ ] Existing threads checked
- [ ] Features parsed from ROADMAP.md
- [ ] Feature picker presented with multi-select
- [ ] Thread named
- [ ] Scope discussed for each feature
- [ ] scope.md created in thread directory
- [ ] STATE.md updated with thread status
- [ ] Changes committed
- [ ] Next steps shown
</success_criteria>
