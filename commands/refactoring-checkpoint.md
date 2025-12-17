---
description: Execute refactors tier by tier from refactor report
---

# Refactoring Checkpoint

Execute refactors from the report. Work only on files listed in Scope.

## Instructions

### 1. Read State

Read CLAUDE.md and extract from `Pipeline State`:
- **Feature**: The feature being refactored
- **Tier**: Current tier (high, medium, or low)
- **Tier-Status**: pending, in-progress, or complete
- **Reports.refactors**: Path to the refactor report

**If no Pipeline State:** STOP and inform user:
```
Cannot proceed: No Pipeline State found.

Run /refactor-hunt-checkpoint first to generate a refactor report.
```

### 2. Load Refactor Report

Read the refactor report from the path in `Reports.refactors`.

Extract:
- **Scope section**: File list (these are the ONLY files you may edit)
- **Current tier's refactors**: From the table matching current Tier

**If refactor report doesn't exist:** STOP and ask user to run `/refactor-hunt-checkpoint`.

### 3. Set Tier In-Progress

If Tier-Status is `pending`, update Pipeline State:
```markdown
Tier-Status: in-progress
```

### 4. Execute Refactors in Current Tier

For each refactor in the current tier's table:

1. **Read the file** at the location specified
2. **Understand the issue** from Issue and Suggested Fix columns
3. **Execute the refactor** using Edit tool
4. **Verify no regressions** — run tests if available
5. **Mark as done** in your tracking

**Constraints:**
- Only edit files listed in Scope
- Do NOT use Glob, Grep, or Explore agents
- Follow the Suggested Fix unless you find a better approach
- If a refactor would break things, skip it with a note

### 5. Run Tests (If Available)

After each refactor (or batch of related refactors):
- Run the project's test suite if one exists
- If tests fail, either fix the issue or revert the refactor
- Note test results in the summary

### 6. Complete Current Tier

When all refactors in the current tier are done (or explicitly skipped):

**If more tiers remain:**
```markdown
## Pipeline State
Phase: refactoring
Tier: [next tier: high→medium, medium→low]
Tier-Status: pending
```

**If all tiers complete (low tier done):**
```markdown
## Pipeline State
Phase: build
Tier: -
Tier-Status: -
Reports:
  - bugs: reports/bugs-[feature].md
  - fixes: reports/fixes-[feature].md
  - refactors: reports/refactors-[feature].md
```

### 7. Run /checkpoint

After completing the tier, execute `/checkpoint` to:
- Update Last Session with refactoring progress
- Generate continuation prompt for next tier or build phase

## Tier Progression

```
high (pending) → high (in-progress) → high (complete)
                                          ↓
                              medium (pending) → medium (in-progress) → medium (complete)
                                                                            ↓
                                                                low (pending) → low (in-progress) → low (complete)
                                                                                                        ↓
                                                                                                  Phase: build
```

## Output Summary

After each tier:
```
Refactoring progress for [feature]:

Tier: [tier] - COMPLETE
- Completed: X refactors
- Skipped: Y refactors (reasons noted)
- Tests: [passed/failed/not available]

Next: [next tier] tier OR Pipeline complete - back to build phase

Files modified:
- [list of changed files]
```

## Guidelines

- Execute one refactor at a time, verify after each
- If a refactor is riskier than expected, skip it with a note
- Keep refactors isolated — don't chain multiple changes together
- Run tests frequently if available
- If no tests exist, be extra careful with verification
- Large (L) effort refactors may span multiple checkpoint cycles — that's OK
- Maintain consistent code style with the existing codebase
