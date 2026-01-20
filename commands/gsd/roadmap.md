---
name: gsd:roadmap
description: Review and realign the project roadmap
allowed-tools:
  - Read
  - Write
  - Bash
  - AskUserQuestion
---

<objective>
Strategic discussion about the roadmap. Review progress, reprioritize, add/remove features.

**Use when:**
- You want to rethink priorities
- Scope has changed
- You learned something that affects the plan
- You want to add new features
- You want to see the big picture

**Not for:** Picking the next thing to work on (use `/gsd:next`)
</objective>

<context>
@.planning/ROADMAP.md
@.planning/STATE.md
</context>

<process>

## 1. Load Current State

Read ROADMAP.md and STATE.md. Parse:
- Total features
- Completed features
- In-progress features
- Remaining features
- Recent decisions/notes

## 2. Present Status

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► ROADMAP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Progress: [████████░░░░] 4/12 features complete

## Completed
✓ User login
✓ Password reset
✓ Dashboard layout
✓ Analytics cards

## In Progress
◆ Activity feed

## Remaining
○ OAuth integration
○ Settings page
○ Dark mode
○ Export data
○ Notifications
○ Search
○ Admin panel
```

## 3. Open Discussion

Use AskUserQuestion to understand what the user wants to do:

```
question: "What would you like to do with the roadmap?"
options:
  - "Add a feature" — I have something new to add
  - "Remove/descope" — Something should come off
  - "Reprioritize" — Change the order of features
  - "Just discuss" — I want to talk through something
```

Based on selection:
- **Add a feature:** Ask what feature, gather details, add to ROADMAP.md
- **Remove/descope:** Show current features, ask which to remove
- **Reprioritize:** Show current order, discuss new priorities
- **Just discuss:** Ask freeform "What's on your mind?" and listen for concerns

Use additional AskUserQuestion calls for specific decisions as needed.

## 4. Make Changes

Based on discussion, offer to:

**Add features:**
```
question: "Add this to the roadmap?"
options:
  - "Yes, add it"
  - "No, skip"
```

**Remove features:**
```
question: "Remove [feature] from roadmap?"
options:
  - "Yes, remove"
  - "No, keep it"
```

**Reorder:**
Present new order, confirm.

**Resize:**
Change feature size (small/medium/large).

## 5. Update Files

**Update ROADMAP.md** with changes:
- Add/remove features
- Reorder as discussed
- Update any details

**Update STATE.md** with decisions:
```markdown
## Decisions
- [date]: Moved notifications before search (user feedback shows this is critical)
- [date]: Removed admin panel from v1 (descoped)
```

## 6. Commit Changes

```bash
git add .planning/ROADMAP.md .planning/STATE.md
git commit -m "docs: update roadmap - [brief description]"
```

## 7. Confirm

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► ROADMAP UPDATED ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Changes:
- [change 1]
- [change 2]

Run /gsd:next to continue building
```

</process>

<success_criteria>
- [ ] Current status clearly presented
- [ ] User's concerns addressed
- [ ] Changes made to ROADMAP.md (if any)
- [ ] Decisions logged in STATE.md
- [ ] Changes committed
</success_criteria>
