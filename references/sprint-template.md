# Sprint Plan Template

A single-file plan for time-boxed builds. Holds context, decisions, tasks, and
progress in one place — no separate requirements/design/tasks docs.

```markdown
# <Feature Name> — Sprint Plan
> Status: draft

## Goal
One or two sentences: what we're building and why. The short description, sharpened.

## Decisions
Captured answers to the clarifying interview — only what shapes the build.
- **D-1**: <decision> (e.g. "Store in-memory; no persistence this build.")
- **D-2**: <decision>
Constraints / non-goals worth pinning go here too, as bullets — don't make a
separate section for one or two of them.

## Tasks (happy-path-first)
Thinnest runnable slice first, then edges. Check off as you go.
- [ ] **1. <Title>** — <what to do> (`path/to/file`) — *happy path*
- [ ] **2. <Title>** — smoke/integration test proving slice 1 runs (`path/to/test`)
- [ ] **3. <Title>** — <edge case / error handling> (`path/to/file`)
- [ ] **4. <Title>** — <next slice> (`path/to/file`)

## Verification
The smoke test: "start the app, hit X, expect Y" or "run suite, all green."
```

## Guidance

- **Goal**: don't restate the prompt verbatim — sharpen it with what the interview
  resolved. One or two sentences.
- **Decisions**: this is the value of the file — the ambiguities you surfaced and
  how they were settled. Stable IDs (`D-1`, …) so tasks can reference them. Skip
  decisions with obvious answers; capture only what a reader couldn't infer.
- **Tasks**: 4-10 items, happy-path-first. The first task(s) build the thinnest
  end-to-end slice; an early task makes it provably runnable (smoke/integration
  test); later tasks layer edge cases. Unit tests still ride with each task. Mark
  the happy-path slice explicitly so the runnable milestone is visible.
- No `[small]/[medium]` complexity tags, no per-task `Spec:`/`Review:` stamps —
  this is the lean path. If you find yourself wanting them, you want the full
  `spec` flow, not `sprint`.
- **Progress lives here**: checking off a task is the only progress record. No
  separate tasks doc. Steers land as inline edits to Goal/Decisions/Tasks.
- Keep the whole file under ~50 lines. If it's growing past that, the scope is too
  big for a sprint — switch to the full `spec` flow.
