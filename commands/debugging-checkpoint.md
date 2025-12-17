---
description: Fix bugs tier by tier from bug report
---

# Debugging Checkpoint

Fix bugs from the bug report. Work only on files listed in the report's Scope.

## Instructions

### 1. Read State

Read CLAUDE.md and extract from `Pipeline State`:
- **Feature**: The feature being debugged
- **Tier**: Current tier (high, medium, or low)
- **Tier-Status**: pending, in-progress, or complete
- **Reports.bugs**: Path to the bug report

**If no Pipeline State:** STOP and inform user:
```
Cannot proceed: No Pipeline State found.

Run /bug-hunt-checkpoint first to generate a bug report and set up the pipeline.
```

### 2. Load Bug Report

Read the bug report from the path in `Reports.bugs`.

Extract:
- **Scope section**: File list (these are the ONLY files you may edit)
- **Current tier's bugs**: From the table matching current Tier

**If bug report doesn't exist:** STOP and ask user to run `/bug-hunt-checkpoint`.

### 3. Set Tier In-Progress

If Tier-Status is `pending`, update Pipeline State:
```markdown
Tier-Status: in-progress
```

### 4. Fix Bugs in Current Tier

For each bug in the current tier's table:

1. **Read the file** at the location specified
2. **Understand the bug** from Description and Impact columns
3. **Fix the bug** using Edit tool
4. **Verify the fix** doesn't introduce new issues
5. **Document the fix** for the Fixes Report

**Constraints:**
- Only edit files listed in Scope
- Do NOT use Glob, Grep, or Explore agents
- Do NOT refactor beyond what's needed for the fix

### 5. Generate/Update Fixes Report

Create or update `reports/fixes-[feature].md`:

```markdown
# Fixes Report: [Feature Name]

Generated: [YYYY-MM-DD]
Bug Report: [source report path]

## Scope
Files analyzed:
- [file1]
- [file2]

## High Priority Fixes
| Bug # | Status | Fix Description | Files Changed |
|-------|--------|-----------------|---------------|
| 1 | fixed | What was done | file1.ts |
| 2 | skipped | Reason for skipping | - |

## Medium Priority Fixes
| Bug # | Status | Fix Description | Files Changed |
|-------|--------|-----------------|---------------|

## Low Priority Fixes
| Bug # | Status | Fix Description | Files Changed |
|-------|--------|-----------------|---------------|

## Summary
- Fixed: X of Y total bugs
- Skipped: Z (with reasons if any)
```

**Copy the Scope section from the bug report** — this propagates to the next phase.

### 6. Complete Current Tier

When all bugs in the current tier are fixed (or explicitly skipped):

**If more tiers remain:**
```markdown
## Pipeline State
Phase: debugging
Tier: [next tier: high→medium, medium→low]
Tier-Status: pending
```

**If all tiers complete (low tier done):**
```markdown
## Pipeline State
Phase: refactor-hunt
Tier: -
Tier-Status: -
Reports:
  - bugs: reports/bugs-[feature].md
  - fixes: reports/fixes-[feature].md
```

### 7. Run /checkpoint

After completing the tier, execute `/checkpoint` to:
- Update Last Session with debugging progress
- Generate continuation prompt for next tier or next phase

## Tier Progression

```
high (pending) → high (in-progress) → high (complete)
                                          ↓
                              medium (pending) → medium (in-progress) → medium (complete)
                                                                            ↓
                                                                low (pending) → low (in-progress) → low (complete)
                                                                                                        ↓
                                                                                              Phase: refactor-hunt
```

## Output Summary

After each tier:
```
Debugging progress for [feature]:

Tier: [tier] - COMPLETE
- Fixed: X bugs
- Skipped: Y bugs (reasons listed in report)

Next: [next tier] tier OR Ready for /refactor-hunt-checkpoint

Report updated: reports/fixes-[feature].md
```

## Guidelines

- Fix one bug at a time, verify each fix
- If a bug can't be fixed without broader changes, mark as "skipped" with reason
- Keep fixes minimal — don't refactor while debugging
- Update the fixes report after each bug, not at the end
- If tests exist, run them after fixing each bug
