# Roadmap Template

Template for `.planning/ROADMAP.md`.

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
**Notes:** [Any specifics]

### [Feature Name]
**Goal:** [What this delivers]
**Size:** [small/medium/large]
```

## Guidelines

- Group related features under headers (just organization, not separate artifact)
- Order by dependencies and priority
- Each feature is a deliverable unit of work
- Size indicates complexity:
  - **small:** hours
  - **medium:** day
  - **large:** days

## Marking Progress

Features start as `- [ ]` and become `- [x]` when complete:

```markdown
### Authentication
- [x] **Login flow** — Email/password authentication
- [x] **Password reset** — Forgot password flow
- [ ] **OAuth** — Google/GitHub login
```
