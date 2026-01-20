<div align="center">

# GET SHIT DONE

**A light-weight and powerful meta-prompting system for Claude Code.**

**Solves context rot — the quality degradation that happens as Claude fills its context window.**

[![npm version](https://img.shields.io/npm/v/get-shit-done-cc?style=for-the-badge&logo=npm&logoColor=white&color=CB3837)](https://www.npmjs.com/package/get-shit-done-cc)
[![npm downloads](https://img.shields.io/npm/dm/get-shit-done-cc?style=for-the-badge&logo=npm&logoColor=white&color=CB3837)](https://www.npmjs.com/package/get-shit-done-cc)
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/glittercowboy/get-shit-done?style=for-the-badge&logo=github&color=181717)](https://github.com/glittercowboy/get-shit-done)

<br>

```bash
npx get-shit-done-cc
```

**Works on Mac, Windows, and Linux.**

<br>

![GSD Install](assets/terminal.svg)

<br>

*"If you know clearly what you want, this WILL build it for you. No bs."*

*"By far the most powerful addition to my Claude Code. Nothing over-engineered. Literally just gets shit done."*

<br>

**Trusted by engineers at Amazon, Google, Shopify, and Webflow.**

[Why I Built This](#why-i-built-this) · [How It Works](#how-it-works) · [Commands](#commands)

</div>

---

## Why I Built This

I'm a solo developer. I don't write code — Claude Code does.

Other spec-driven development tools exist; BMAD, Speckit... But they all make things way more complicated than they need to be. Sprint ceremonies, story points, stakeholder syncs, retrospectives, Jira workflows. I'm not a 50-person software company. I don't want to play enterprise theater. I'm just a creative person trying to build great things.

So I built GSD. **5 commands.** That's it.

The system gives Claude everything it needs to do the work. I trust the workflow. It just does a good job.

— **TÂCHES**

---

## Getting Started

```bash
npx get-shit-done-cc
```

Verify with `/gsd:help` inside Claude Code.

### Recommended: Skip Permissions Mode

```bash
claude --dangerously-skip-permissions
```

> [!TIP]
> This is how GSD is intended to be used — stopping to approve `git commit` 50 times defeats the purpose.

---

## How It Works

### 1. Initialize

```
/gsd:init
```

One command takes you from idea to ready-to-build:

1. **Q&A** — Asks until it understands what you're building
2. **Research** — Investigates the domain (optional)
3. **Roadmap** — Creates features grouped and ordered

**Creates:** `ROADMAP.md`, `STATE.md`

---

### 2. Build

```
/gsd:next
```

The main loop. Repeat until done:

1. **Shows progress** — Where you are
2. **Presents options** — Pick a feature (or group of features)
3. **Discussion** — Captures your vision
4. **Research** — Investigates implementation (optional)
5. **Plan** — Creates implementation plan (native plan mode)
6. **Execute** — Builds it with atomic commits

**Creates:** `CONTEXT.md`, `RESEARCH.md`, `PLAN.md`

---

### 3. Repeat

```
/gsd:next
/gsd:next
/gsd:next
...done
```

Each feature gets your input, proper research, clean execution. Context stays fresh. Quality stays high.

---

### Realign

```
/gsd:roadmap
```

When priorities change:
- Rethink what matters
- Add/remove features
- Reorder the roadmap

---

### Quick Tasks

```
/gsd:quick
```

For ad-hoc stuff outside the roadmap:
- Bug fixes
- Config changes
- One-off adjustments

Same guarantees (atomic commits, tracking) but faster.

---

### Debug

```
/gsd:debug "something is broken"
```

Systematic debugging with persistent state. Survives `/clear` so you can pick up where you left off.

---

## Commands

| Command | Purpose |
|---------|---------|
| `/gsd:init` | Initialize project |
| `/gsd:next` | Select → plan → build |
| `/gsd:roadmap` | Realign priorities |
| `/gsd:quick` | Ad-hoc tasks |
| `/gsd:debug` | Systematic debugging |

**That's it. 5 commands.**

---

## Files

```
.planning/
├── ROADMAP.md          ← features (grouped, ordered)
├── STATE.md            ← project memory
├── features/           ← per-feature artifacts
│   └── {feature}/
│       ├── CONTEXT.md  ← your vision
│       ├── RESEARCH.md ← implementation research
│       └── PLAN.md     ← the plan
├── quick/              ← quick tasks
└── debug/              ← debug sessions
```

---

## Why It Works

### Native Integration

GSD uses Claude Code's native features:
- **Plan mode** for implementation planning
- **TodoWrite** for task tracking
- **AskUserQuestion** for Q&A

No custom agents reinventing the wheel.

### Context Engineering

State persists in files. After `/clear`, Claude reads ROADMAP.md and STATE.md to know exactly where you are.

### Atomic Commits

Every task gets its own commit. Git bisect works. History is clean.

---

## Troubleshooting

**Commands not found?**
- Restart Claude Code
- Re-run `npx get-shit-done-cc`

**Updating?**
```bash
npx get-shit-done-cc@latest
```

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

<div align="center">

**Claude Code is powerful. GSD makes it reliable.**

</div>
