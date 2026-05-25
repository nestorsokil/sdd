# Requirements Template

```markdown
# <Feature Name> — Requirements
> Status: draft

## Overview
One paragraph: what this feature does and why it matters.

## Behavior
- Describe each user-visible behavior as a concrete scenario.
- Use acceptance-criteria style: given X, when Y, then Z.
- Keep each item to 1-2 sentences.
- **Give each criterion a stable ID** (`AC-1`, `AC-2`, …) so tasks and tests
  can reference it without ambiguity. IDs survive renames; bullet positions
  don't.

## Constraints
- Performance, security, compatibility, or regulatory boundaries.
- Things that limit how the feature can be built.
- **Stable IDs** (`C-1`, `C-2`, …) for the same reason as acceptance criteria.

## Non-goals
- What this feature explicitly does NOT do.
- Prevents scope creep during design and implementation.
- **Stable IDs** (`NG-1`, `NG-2`, …).
```

## Guidance

- If the user provides a vague description ("I want a caching layer"), ask clarifying
  questions before writing: who uses it, what triggers it, what happens on failure,
  what are the performance expectations.
- Don't pad. If the feature is "add a health check endpoint", 15 lines is fine.
- Acceptance criteria should be things you could write a test for.
