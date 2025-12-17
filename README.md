# Claude Code Workflow Guide

Persistent context management and code quality pipelines for Claude Code sessions.

---

## Quick Install

Tell Claude:

```
Install the workflow commands from https://github.com/creativeprofit22/claude-workflow-guide

Copy all files from the commands/ folder to ~/.claude/commands/
```

That's it. You now have `/checkpoint` and the full pipeline.

---

## What You Get

| Command | Purpose |
|---------|---------|
| `/checkpoint` | Save progress to CLAUDE.md + generate continuation prompt |
| `/bug-hunt-checkpoint` | Analyze files for bugs, produce prioritized report |
| `/debugging-checkpoint` | Fix bugs tier by tier (High → Medium → Low) |
| `/refactor-hunt-checkpoint` | Find refactoring opportunities, produce report |
| `/refactoring-checkpoint` | Execute refactors tier by tier |

---

## The Pipeline

```
/bug-hunt-checkpoint → /debugging-checkpoint → /refactor-hunt-checkpoint → /refactoring-checkpoint
                                    ↓
                            /checkpoint (hand-off between any phase)
```

Each command:
- Reads scope from the previous phase's report
- Works only on tracked files (no random exploration)
- Updates Pipeline State in CLAUDE.md
- Hands off via `/checkpoint`

---

## What /checkpoint Does

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
Files: [specific files - required for pipeline commands]

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

## Using the Pipeline

### 1. Set Up Current Focus

In your project's `CLAUDE.md`:

```markdown
## Current Focus
Section: Authentication
Files: src/auth/login.ts, src/auth/session.ts, src/middleware/auth.ts
```

### 2. Run the Pipeline

```bash
/bug-hunt-checkpoint     # Analyzes files, creates reports/bugs-auth.md
/checkpoint              # Hand off, get continuation prompt

# After fixing bugs...
/debugging-checkpoint    # Fixes bugs tier by tier
/checkpoint

# After all bugs fixed...
/refactor-hunt-checkpoint  # Finds refactor opportunities
/checkpoint

# Execute refactors...
/refactoring-checkpoint
/checkpoint              # Back to build phase
```

### 3. Reports

The pipeline generates reports in `reports/`:

```
reports/
├── bugs-[feature].md      # Prioritized bug list
├── fixes-[feature].md     # What was fixed
└── refactors-[feature].md # Refactoring opportunities
```

---

## Pipeline State

When in the pipeline, CLAUDE.md gets a Pipeline State section:

```markdown
## Pipeline State
Phase: debugging
Feature: Authentication
Tier: high
Tier-Status: in-progress
Reports:
  - bugs: reports/bugs-auth.md
  - fixes: reports/fixes-auth.md
```

`/checkpoint` reads this and generates pipeline-aware continuation prompts.

---

## Working on Specific Sections

Instead of loading entire project context:

```
Let's work on the auth module. Focus on docs/auth.md and src/auth/
```

Claude only reads those files. Minimal tokens.

---

## Multi-Project Setup

Keep a note with your active projects:

```
Project A: /home/user/projects/project-a
Project B: /home/user/projects/project-b
```

Paste the right path when starting a chat.

---

## Project Structure for Efficiency

```
/your-project
├── CLAUDE.md           # High-level index + pipeline state
├── reports/            # Generated by pipeline commands
│   ├── bugs-*.md
│   ├── fixes-*.md
│   └── refactors-*.md
├── docs/
│   ├── module-a.md     # Deep dive per section
│   └── module-b.md
├── src/
│   ├── module-a/
│   └── module-b/
```

---

## Manual Installation

If you prefer to set it up yourself:

1. Create `~/.claude/commands/` directory
2. Copy all files from `commands/` folder in this repo
3. Done — all commands are now available in your Claude Code sessions

---

## Quick Reference

| Action | What to Do |
|--------|------------|
| Start session | `Let's work on [path]` |
| Check context usage | `/context` |
| Save progress | `/checkpoint` |
| Clear and continue | Copy prompt → `/clear` → paste |
| Hunt bugs | `/bug-hunt-checkpoint` |
| Fix bugs | `/debugging-checkpoint` |
| Find refactors | `/refactor-hunt-checkpoint` |
| Do refactors | `/refactoring-checkpoint` |

---

## Integration with /fix

If you have `/minimal-claude:setup-code-quality` installed, the pipeline integrates:

1. `/bug-hunt-checkpoint` checks for lint/type errors first
2. Recommends running `/fix` to clear mechanical issues
3. Keeps bug reports focused on real logic bugs

---

## Why This Works

- **No repeated explanations** - docs hold context
- **Minimal token usage** - read only what's needed
- **Clean handoffs** - next chat picks up instantly
- **Nothing breaks** - interfaces documented
- **Systematic quality** - bugs and refactors tracked by priority

---

*One command. Context managed. Quality assured.*
