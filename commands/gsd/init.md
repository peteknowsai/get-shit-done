---
name: gsd:init
description: Initialize a new GSD project with roadmap
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
---

<objective>
Initialize a GSD project through conversational flow.

**Flow:** Check existing code → Q&A → Research (optional) → Create roadmap

**Creates:**
- `.planning/ROADMAP.md` — Features grouped and ordered
- `.planning/STATE.md` — Project memory
- `.planning/config.json` — Preferences
- `.planning/codebase/` — Codebase analysis (if existing code)
- `.planning/research/` — Domain research (if selected)

**After this:** Run `/gsd:next` to start building.
</objective>

<process>

## 1. Check Environment

```bash
# Abort if already initialized
[ -f .planning/ROADMAP.md ] && echo "ERROR: Already initialized. Use /gsd:next or /gsd:roadmap" && exit 1

# Initialize git if needed
[ -d .git ] || git init
```

## 2. Detect Existing Code (Brownfield)

```bash
CODE_FILES=$(find . -name "*.ts" -o -name "*.js" -o -name "*.py" -o -name "*.go" -o -name "*.rs" 2>/dev/null | grep -v node_modules | grep -v .git | head -20)
HAS_CODE=$([ -n "$CODE_FILES" ] && echo "yes" || echo "no")
```

**If existing code detected:**

Use AskUserQuestion:
```
question: "I see existing code. Map the codebase first?"
options:
  - "Yes, analyze it (Recommended)" — Understand patterns before planning
  - "No, skip" — I'll explain what matters
```

**If "Yes":** Spawn parallel codebase mapper agents:
- Stack analysis → `.planning/codebase/STACK.md`
- Architecture analysis → `.planning/codebase/ARCHITECTURE.md`
- Conventions analysis → `.planning/codebase/CONVENTIONS.md`

## 3. Deep Questioning

Display:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► INIT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Ask freeform: **"What do you want to build?"**

Follow threads naturally:
- What excited them about this idea
- What problem it solves
- Who it's for
- What's the core thing that must work
- Technical preferences or constraints

Use AskUserQuestion for specific choices when needed.

**Keep going until you could write a clear roadmap.** Then:

Use AskUserQuestion:
```
question: "I think I understand. Ready to create the roadmap?"
options:
  - "Create roadmap" — Let's go
  - "Keep talking" — I have more to share
```

## 4. Research Decision

Use AskUserQuestion:
```
question: "Research the domain before creating roadmap?"
options:
  - "Yes, research (Recommended)" — Discover standard approaches
  - "No, skip" — I know this domain
```

**If "Yes":** Spawn parallel researcher agents:
- Stack research → `.planning/research/STACK.md`
- Patterns research → `.planning/research/PATTERNS.md`
- Pitfalls research → `.planning/research/PITFALLS.md`

Present key findings before continuing.

## 5. Create Roadmap

Based on Q&A and research, create `.planning/ROADMAP.md`:

```markdown
# Roadmap

## Overview
[One paragraph: what we're building and why]

## Features

### [Group Name]
- [ ] **[Feature Name]** — [One-line description]
- [ ] **[Feature Name]** — [One-line description]

### [Group Name]
- [ ] **[Feature Name]** — [One-line description]

## Feature Details

### [Feature Name]
**Goal:** [What this delivers]
**Size:** [small/medium/large]
**Notes:** [Any specifics from Q&A]
```

**Guidelines:**
- Group related features under headers
- Order by dependencies and priority
- Each feature is a deliverable unit of work
- Size indicates complexity (small: hours, medium: day, large: days)

Present the roadmap and use AskUserQuestion:
```
question: "Does this roadmap look right?"
options:
  - "Approve" — Let's build
  - "Adjust" — I want to change something
```

**If "Adjust":** Get feedback, revise, re-present.

## 6. Create State File

Create `.planning/STATE.md`:

```markdown
# Project State

## Current Position
**Status:** Ready to start
**Next:** [First feature name]

## Decisions
[None yet]

## Notes
[None yet]
```

## 7. Create Config

Create `.planning/config.json`:

```json
{
  "created": "[date]",
  "version": "2.0"
}
```

## 8. Update CLAUDE.md

Add GSD reference to project's CLAUDE.md so any Claude session knows GSD is available:

```markdown
## GSD (Get Shit Done)

This project uses GSD for structured development.

**Commands:**
- `/gsd:next` — Pick next feature, plan, execute
- `/gsd:roadmap` — Review/update roadmap
- `/gsd:quick` — Fast tasks outside the roadmap
- `/gsd:debug` — Systematic debugging

**State:** `.planning/` (gitignored)
```

**If CLAUDE.md exists:** Append the GSD section at the end.
**If no CLAUDE.md:** Create it with just the GSD section.

## 9. Commit

```bash
mkdir -p .planning/features
git add .planning/ CLAUDE.md
git commit -m "docs: initialize GSD project"
```

## 10. Done

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► READY ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[N] features mapped

Run /gsd:next to start building
```

</process>

<success_criteria>
- [ ] .planning/ directory created
- [ ] Codebase mapped (if existing code)
- [ ] Q&A completed
- [ ] Research completed (if selected)
- [ ] ROADMAP.md created with features
- [ ] STATE.md initialized
- [ ] CLAUDE.md updated with GSD section
- [ ] Committed to git
</success_criteria>
