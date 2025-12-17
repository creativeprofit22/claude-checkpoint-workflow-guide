---
description: Find refactoring opportunities, produce prioritized report (High/Med/Low)
---

# Refactor Hunt Checkpoint

Find refactoring opportunities. Work only on files from the previous report's Scope.

## Instructions

### 1. Get Scope

Read CLAUDE.md and extract from `Pipeline State`:
- **Reports.fixes**: Path to fixes report (preferred)
- **Reports.bugs**: Path to bug report (fallback if no fixes)

Load the report and extract the **Scope section** (file list).

**If no Pipeline State or reports:** STOP and inform user:
```
Cannot proceed: No Pipeline State or reports found.

Either:
1. Run /bug-hunt-checkpoint to start the pipeline
2. Or set up Current Focus with files and run /bug-hunt-checkpoint
```

### 2. Read Each File

- Use the Read tool on each file in scope
- Do NOT use Glob, Grep, or Explore agents
- If a file doesn't exist, note it and continue with others

### 3. Analyze for Refactors

Look for these opportunities:

| Category | What to Find |
|----------|--------------|
| DRY violations | Duplicated code, copy-paste patterns |
| Complexity | Long functions, deep nesting, high cyclomatic complexity |
| Naming | Unclear variable/function names, misleading names |
| Abstractions | Missing interfaces, god objects, feature envy |
| Patterns | Inconsistent coding patterns within the feature |
| Dead code | Unused variables, unreachable code, commented-out code |
| Type safety | Weak typing, excessive `any`, missing generics |

### 4. Categorize by Priority

| Priority | Criteria |
|----------|----------|
| **High** | Tech debt blocking progress, DRY violations, high complexity |
| **Medium** | Code clarity, maintainability, inconsistent patterns |
| **Low** | Style consistency, minor improvements, nice-to-have |

### 5. Estimate Effort

For each refactor, estimate effort:
- **S (Small)**: Quick fix, single location, < 30 min
- **M (Medium)**: Some thought needed, few locations, < 2 hours
- **L (Large)**: Significant change, multiple files, > 2 hours

### 6. Generate Refactor Report

Create `reports/refactors-[feature].md`:

```markdown
# Refactor Report: [Feature Name]

Generated: [YYYY-MM-DD]
Feature: [name] (1 of N)
Source: [fixes or bugs report path]

## Scope
Files analyzed:
- [file1]
- [file2]

## High Priority (Tech Debt / DRY)
| # | Location | Issue | Suggested Fix | Effort |
|---|----------|-------|---------------|--------|
| 1 | file:line | Problem description | How to fix | S/M/L |

## Medium Priority (Code Clarity)
| # | Location | Issue | Suggested Fix | Effort |
|---|----------|-------|---------------|--------|

## Low Priority (Nice-to-Have)
| # | Location | Issue | Suggested Fix | Effort |
|---|----------|-------|---------------|--------|

## Summary
- High: X refactors (Y Small, Z Medium, W Large)
- Medium: X refactors
- Low: X refactors
- Total: N refactors
```

**Copy the Scope section from the source report** — this maintains the file chain.

### 7. Update Pipeline State

Update CLAUDE.md:

```markdown
## Pipeline State
Phase: refactoring
Feature: [name]
Tier: high
Tier-Status: pending
Reports:
  - bugs: reports/bugs-[feature].md
  - fixes: reports/fixes-[feature].md
  - refactors: reports/refactors-[feature].md
```

### 8. Run /checkpoint

After completing the report, execute `/checkpoint` to:
- Update Last Session with refactor hunt summary
- Generate continuation prompt for refactoring phase

## Output Summary

When complete, confirm:
```
Refactor hunt complete for [feature].

Report: reports/refactors-[feature].md
- High: X refactors
- Medium: Y refactors
- Low: Z refactors

Pipeline state updated. Ready for /refactoring-checkpoint.
```

## Guidelines

- Focus on actionable refactors, not theoretical improvements
- Don't flag things that are "just different" — only actual issues
- Include specific file:line references
- Be realistic with effort estimates
- If no refactors found in a tier, leave the table empty (don't remove it)
- Prioritize refactors that would make future work easier
