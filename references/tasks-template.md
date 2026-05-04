# Tasks Template

```markdown
# <Feature Name> — Tasks
> Status: draft

## Prerequisites
Any setup needed before implementation (branch, dependencies, config).

## Tasks
- [ ] [small] **1. <Title>** — <what to do> (`path/to/file`)
  - Spec: requirements §<id>, design §<section>
- [ ] [medium] **2. <Title>** — <what to do> (`path/to/file`)
  - Spec: design §<section>
- [ ] [small] **3. <Title>** — <what to do> (`path/to/test/file`)
  - Spec: requirements §<id> (acceptance criterion)

## Verification
How to verify the feature works end-to-end after all tasks are complete.
```

## Guidance

- Each task = one logical change that can be committed independently.
  "Create entity, repository, service, and controller" is four tasks.
- File paths are critical — they turn abstract plans into executable instructions.
- Complexity hints guide review cadence:
  - `[small]` — boilerplate, config, simple wiring. Can chain these.
  - `[medium]` — real logic, non-trivial integration. Review after each.
  - `[large]` — architectural changes, complex migrations. Pause and discuss.
- Tasks should build on each other so you can verify incrementally.
- **Spec touchpoints**: each task lists which requirement IDs and design sections
  it implements. Makes drift detection cheap — when a task changes mid-flight,
  the touched spec sections to update are already known. Tasks with no spec
  touchpoint are scope creep; either drop them or amend the spec.
- Verification = the smoke test: "start the app, hit endpoint, expect response"
  or "run test suite, all green."
