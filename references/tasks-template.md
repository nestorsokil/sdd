# Tasks Template

```markdown
# <Feature Name> — Tasks
> Status: draft

## Prerequisites
Any setup needed before implementation (branch, dependencies, config).

## Tasks
- [ ] [small] **1. <Title>** — <what to do> (`path/to/file.java`)
- [ ] [medium] **2. <Title>** — <what to do> (`path/to/file.java`)
- [ ] [small] **3. <Title>** — <what to do> (`path/to/test/FileTest.java`)

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
- Verification = the smoke test: "start the app, hit endpoint, expect response"
  or "run test suite, all green."
