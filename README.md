# Claude Code Workflow Guide

Persistent context management for Claude Code sessions.

---

## Quick Install

Tell Claude:

```
Set up the checkpoint command from https://github.com/creativeprofit22/claude-workflow-guide
```

That's it. Claude will create `~/.claude/commands/checkpoint.md` for you.

---

## What This Does

The `/checkpoint` command solves two problems:

1. **Context bloat** — When your session gets too long, you lose context. `/checkpoint` generates a continuation prompt so you can `/clear` and keep going.

2. **Session continuity** — Progress is saved to `CLAUDE.md` in your project root. Next time you open Claude in that project, it picks up where you left off.

---

## How It Works

Run `/checkpoint` and two things happen:

1. **CLAUDE.md gets updated** — Your progress is saved to disk
2. **A continuation prompt appears** — Copy it if you need to clear context

Then you can:
- **Clear now**: Copy prompt → `/clear` → paste → keep working
- **Stop for the day**: Just close. CLAUDE.md has everything for tomorrow
- **Keep working**: Do nothing, state is saved anyway

---

## Usage

### Starting a Session

```
Let's work on /path/to/your/project
```

Claude reads the `CLAUDE.md` file and knows the project state.

### During Work

When context gets high (check with `/context`):

```
/checkpoint
```

Copy the continuation prompt, run `/clear`, paste, continue.

### Ending a Session

```
/checkpoint
```

Close the terminal. Tomorrow, start fresh and CLAUDE.md brings you back up to speed.

---

## The Continuation Prompt

When you run `/checkpoint`, you get something like:

```
## Continuation Prompt

Continue work on MyProject at /home/user/myproject.

**What's Done**: [1-2 bullets of recent progress]

**Current State**: [where you left off]

**Next Step**: [the immediate next thing to do]

**Key Files**: [2-3 relevant file paths]

**Key Context**: [critical info to remember]

**Approach**: Do NOT explore the full codebase. Use the context above.
```

This is designed to be minimal but sufficient — CLAUDE.md has the full details.

---

## CLAUDE.md Template

The command creates this structure if it doesn't exist:

```markdown
# Project Name

One-line description.

## Current Focus
Section: [current area of work]
Files: [relevant files]

## Last Session ([date])
- What was done
- Stopped at: [where you left off]

## Next Steps
1. [First thing to do]
2. [Second thing]
3. [Third thing]
```

You can expand this with additional sections as needed (interfaces, modules to avoid, etc).

---

## Manual Installation

If you prefer to set it up yourself:

1. Create `~/.claude/commands/checkpoint.md`
2. Copy the contents from [checkpoint.md](checkpoint.md)
3. Done — `/checkpoint` is now available in all your Claude Code sessions

---

## Quick Reference

| Action | Command |
|--------|---------|
| Start session | `Let's work on [path]` |
| Check context usage | `/context` |
| Save progress | `/checkpoint` |
| Clear and continue | Copy prompt → `/clear` → paste |

---

*One command. Context managed.*
