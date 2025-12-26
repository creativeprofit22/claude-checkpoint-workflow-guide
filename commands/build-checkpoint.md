---
description: Prepare build, ask execution options, output continuation for implementation
---

# Build Checkpoint

Prepare to implement a feature. Ask execution options, then output continuation prompt for implementation.

## Instructions

### 1. Execution Options (Ask First)

Ask the user:

**Question 1 - Parallel Execution:**
> Run with parallel agents? (Multiple Task agents work concurrently on different parts of the feature)
> - Yes - Spawn parallel agents
> - No - Sequential execution (default)

**Question 2 - Ultra Think Mode:**
> Enable ultra think mode? (Extended reasoning before implementation)
> - Yes - Deep analysis, thorough planning before coding
> - No - Standard execution (default)

Store the user's choices for the continuation prompt.

---

### 2. Get Current Feature

Read CLAUDE.md. Look for:

```markdown
## Pipeline State
Phase: build
Feature: [current feature name]
Features-Remaining: [list or count]
```

**If no Pipeline State or Phase != build:**
- Look for `## Features` or `## Next Steps` to find what to build
- If nothing found, ask user: "What feature should I implement?"

### 3. Get Scope

Extract from CLAUDE.md:
- `Current Focus > Files` — files to work on
- Or infer from feature description

**If scope unclear:** Ask user for key file paths.

### 4. Output Continuation Prompt for Implementation

**Do NOT implement yet.** Output the continuation prompt with execution options embedded:

```
## Continuation Prompt

Continue work on [Project] at [directory].

**Phase**: build (implementation)
**Feature**: [feature name]

**Execution Options**:
- Parallel: [Yes/No - from user choice]
- Ultra Think: [Yes/No - from user choice]

**Scope** (work only on these files):
- [file1]
- [file2]

**What to Build**:
[Brief description of the feature]

**Next Action**: Implement the feature.
- If Parallel=Yes: Spawn Task agents for independent components
- If Ultra Think=Yes: Deep analysis before each implementation decision

After implementation complete, run /validate-checkpoint to begin validation.

**Approach**: Read only the files in Scope. Implement what's needed, nothing more.
```

**STOP.** Do not implement. Wait for user to clear context and paste prompt.

## Output Summary

```
Build checkpoint ready for: [feature name]

Execution options:
- Parallel: [Yes/No]
- Ultra Think: [Yes/No]

Scope:
- [files]

Copy the continuation prompt, clear context, and paste to begin implementation.
```

## Guidelines

- This command prepares, it does NOT implement
- Implementation happens after context clear
- Execution options are embedded in the continuation prompt
- Keep scope focused and explicit
