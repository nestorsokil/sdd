# Research Template

```markdown
# <Feature Name> — Research

## Question
What design decision or trade-off is being explored?

## Options

### Option A: <Name>
Brief description.

**Pros:**
- ...

**Cons:**
- ...

### Option B: <Name>
Brief description.

**Pros:**
- ...

**Cons:**
- ...

## Decision
**Chosen: Option X** — one paragraph explaining why this option won given the
constraints, requirements, and trade-offs above.

## References
- Any external resources, prior art, or benchmarks that informed the decision.
  (Omit if none.)
```

## Guidance

- Only create this file when real back-and-forth happened during spec creation.
  Routine decisions belong in design.md's "Alternatives considered" section.
- The question should be specific. "How should we cache?" is too broad.
  "Should we use a write-through or write-behind cache given our consistency
  requirements?" is the right granularity.
- Keep options honest — steelman each one. A strawman option wastes everyone's time.
- The decision section should be short. If it's long, the question was too broad.
