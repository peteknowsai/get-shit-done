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

Ask freeform: **"What's on your mind about the roadmap?"**

Listen for:
- Reprioritization ("I think we should do X before Y")
- Scope changes ("Actually we don't need X")
- New features ("I realized we need X")
- Concerns ("I'm worried about X")
- Questions ("Should we do X or Y?")

Use AskUserQuestion for specific decisions when helpful.

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
