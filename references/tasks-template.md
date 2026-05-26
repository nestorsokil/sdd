# Tasks Template

```markdown
# <Feature Name> — Tasks
> Status: draft

## Prerequisites
Any setup needed before implementation (branch, dependencies, config).

## Tasks
- [ ] [small] **1. <Title>** — <what to do> (`path/to/file`)
  - Spec: requirements §<id>, design §<section>
  - Review: —
- [ ] [medium] **2. <Title>** — <what to do> (`path/to/file`)
  - Spec: design §<section>
  - Review: —
- [ ] [small] **3. <Title>** — <what to do> (`path/to/test/file`)
  - Spec: requirements §<id> (acceptance criterion)
  - Review: —

## Verification
How to verify the feature works end-to-end after all tasks are complete.
```

## Guidance

- Each task = one logical change that can be committed independently, leaving the
  repo in a working, tested state (builds, pre-existing tests green, new logic
  covered by a unit test in the same change). "Create entity, repository, service,
  and controller" is four tasks.
- **Unit tests ride with their task** — do not add a trailing "write tests" task.
  **An integration test rides with each service-facing task too**: black-box /
  out-of-service preferred (start the real service, hit its real interface), with
  in-process integration (Spring or other in-process harness) as the fallback when
  black-box is clumsy (CLI, library, embedded). Never mock-stub the unit's own
  collaborators. Extend the existing integration suite rather than building new
  infrastructure. Pure wiring/config or non-service-facing tasks are exempt.
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
- **Review stamp**: each task carries a `Review:` line, set during implementation —
  `—` (not yet implemented), `pending` (shipped, self-review deferred), or
  `passed` (self-review ran clean, or with accepted findings noted). Lets you ship
  fast and batch-review later (`/sdd review <name>`) without losing track of what
  is still unreviewed.
- Verification = the smoke test: "start the app, hit endpoint, expect response"
  or "run test suite, all green."
