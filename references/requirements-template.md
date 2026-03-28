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

## Constraints
- Performance, security, compatibility, or regulatory boundaries.
- Things that limit how the feature can be built.

## Non-goals
- What this feature explicitly does NOT do.
- Prevents scope creep during design and implementation.

## Open questions
- Anything unresolved that needs input before design begins.
- Remove this section once all questions are answered.
```

## Guidance

- If the user provides a vague description ("I want a caching layer"), ask clarifying
  questions before writing: who uses it, what triggers it, what happens on failure,
  what are the performance expectations.
- Don't pad. If the feature is "add a health check endpoint", 15 lines is fine.
- Acceptance criteria should be things you could write a test for.
