---
name: gsd:debug
description: Systematic debugging with persistent state
argument-hint: "[issue description]"
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
Debug issues systematically. State persists across `/clear` so you can pick up where you left off.

**Use when:**
- Something's broken and you're not sure why
- You've tried fixing it but it keeps coming back
- You need to investigate methodically

**Flow:**
1. Gather symptoms
2. Form hypothesis
3. Test hypothesis
4. Repeat until root cause found
5. Fix it
</objective>

<context>
User's issue: $ARGUMENTS

Check for active debug sessions:
```bash
ls .planning/debug/*.md 2>/dev/null | grep -v resolved | head -5
```
</context>

<process>

## 1. Check Active Sessions

```bash
mkdir -p .planning/debug .planning/debug/resolved
active=$(ls .planning/debug/*.md 2>/dev/null | grep -v resolved)
```

**If active sessions exist AND no $ARGUMENTS:**

Display the header, then use AskUserQuestion with dynamic options based on active sessions:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► DEBUG
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

```
question: "Which debug session?"
options:
  - "[session-1-slug]" — [current status from file]
  - "[session-2-slug]" — [current status from file]
  - "New issue" — Start a fresh debug session
```

(Build options dynamically from active session files. Max 3 sessions shown + "New issue" option.)

If a session is selected → read that debug file and resume investigation
If "New issue" → proceed to gather symptoms

**If $ARGUMENTS provided:** Start new session.

## 2. Gather Symptoms (New Session)

Ask about:
1. **What should happen?** (expected behavior)
2. **What happens instead?** (actual behavior)
3. **Any error messages?**
4. **When did this start?**
5. **How do you trigger it?**

Keep it conversational, not interrogation.

## 3. Create Debug File

```bash
slug=$(echo "$ISSUE" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9]/-/g' | cut -c1-30)
DEBUG_FILE=".planning/debug/${slug}.md"
```

Write initial state:

```markdown
# Debug: [Issue Summary]

**Started:** [date]
**Status:** investigating

## Symptoms
- Expected: [what should happen]
- Actual: [what happens]
- Errors: [any error messages]
- Trigger: [how to reproduce]
- Timeline: [when it started]

## Hypotheses

### Hypothesis 1: [description]
**Status:** testing
**Evidence for:**
**Evidence against:**
**Tests to run:**

## Investigation Log
- [timestamp] Started investigation
```

## 4. Investigate

Use scientific method:

1. **Form hypothesis** based on symptoms
2. **Design test** to prove/disprove
3. **Run test** and record results
4. **Update debug file** with findings
5. **Repeat** until root cause found

Update debug file as you go:

```markdown
### Hypothesis 1: Database connection timeout
**Status:** disproved
**Evidence against:** Connection logs show successful connects
**Conclusion:** Not the issue

### Hypothesis 2: Race condition in auth check
**Status:** confirmed
**Evidence for:** Added logging, saw auth check completing after render
**Tests:** Added delay, issue reproduces consistently
```

## 5. Root Cause Found

When you've confirmed the root cause:

```markdown
## Root Cause
[Clear explanation of what's wrong and why]

## Fix
[What needs to change]
```

Ask user:
```
question: "Found the root cause. How do you want to proceed?"
options:
  - "Fix it now" — Apply the fix
  - "Add to roadmap" — Plan a proper fix later
  - "I'll fix it manually" — Done investigating
```

## 6. Apply Fix (If Selected)

Fix the issue. Use TodoWrite to track:

```
TodoWrite([
  { content: "Apply fix", status: "in_progress", activeForm: "Fixing the issue" },
  { content: "Verify fix", status: "pending", activeForm: "Verifying the fix" }
])
```

After fix verified, commit:
```bash
git add [files]
git commit -m "fix: [issue description]"
```

## 7. Resolve Session

Move debug file to resolved:
```bash
mv .planning/debug/${slug}.md .planning/debug/resolved/${slug}.md
```

Update the file:
```markdown
**Status:** resolved
**Fixed:** [date]
**Commit:** [hash]

## Resolution
[What was done to fix it]
```

## 8. Done

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► DEBUG RESOLVED ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Issue summary]

Root cause: [explanation]
Fix: [what was done]
Commit: [hash]

Back to work: /gsd:next
```

</process>

<resuming>
Debug state persists in `.planning/debug/[slug].md`.

After `/clear`, run `/gsd:debug` with no arguments to see active sessions and resume.

The debug file contains:
- All symptoms gathered
- All hypotheses tested
- Current investigation state
- What to try next
</resuming>

<success_criteria>
- [ ] Symptoms gathered
- [ ] Debug file created
- [ ] Hypotheses tested methodically
- [ ] Root cause identified
- [ ] Fix applied (if selected)
- [ ] Session resolved
</success_criteria>
