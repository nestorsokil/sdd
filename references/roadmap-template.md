# Roadmap Template

```markdown
# <Project Name> — Roadmap
> Status: draft

## Vision
One paragraph: what we're building and why. The outcome, not the implementation.

## Scope & non-goals
- **In scope**: the broad capabilities this project delivers.
- **Out of scope**: what we are explicitly not building (prevents feature sprawl).

## Project constraints
- Stack, deadlines, platform, and cross-cutting rules that apply to the whole
  project. Each feature inherits these as starting constraints in its
  `1-requirements.md`.
- **Stable IDs** (`PC-1`, `PC-2`, …) so feature specs can cite them.

## Features
At-a-glance index. Each row becomes a `/sdd spec <feature>` run and a
`specs/NNN-<feature>/` set.

| NNN | Feature | Purpose | Depends on | Status |
|-----|---------|---------|------------|--------|
| 001 | <name>  | one line | — | planned |
| 002 | <name>  | one line | 001 | planned |
| 003 | <name>  | one line | 001 | planned |

Status: `planned` (in roadmap only) → `specced` (spec set approved) →
`implemented` (all tasks shipped).

## Sequencing notes
- Foundational / cross-cutting work first (data model, auth, shared infra).
- Dependency ordering — what unblocks what.
- **MVP boundary**: which features are the first shippable slice; which are later
  milestones.

## Per-feature detail (optional)
Only for features whose scope needs more than the one-line purpose above.

### 001 — <name>
- Scope sketch: a few bullets on what's in/out for this feature specifically.
- Notable risk or open question, if any.
```

## Guidance

- The roadmap is the project-level map and a **living index** — it stays in the
  repo and tracks per-feature status over the life of the project. It is not
  thrown away after the first spec.
- Decompose into **feature-sized** chunks, each a candidate for the 3-doc spec
  flow. A good feature is independently spec-able and roughly 5-15 tasks once
  broken down. If a row would clearly be one task, fold it into a neighbor; if it
  would be 30 tasks, split it.
- **Slice vertically.** Each feature should be a deployable increment — a thin
  path through the whole stack that delivers user-observable value on its own.
  Avoid horizontal layers ("all DB work", then "all API work") that ship nothing
  until a sibling lands. When a feature is complete, the system should be
  shippable.
- Works for **greenfield and brownfield** alike. In brownfield, a feature is a
  vertical slice through the *existing* system — name the modules/seams it
  touches in its detail block.
- Order by dependency, foundational work first. Cross-cutting concerns (auth,
  persistence, shared types) that genuinely can't be sliced vertically may be
  early features others depend on — call them out as such rather than defaulting
  to layer-by-layer splits.
- Keep it readable. A typical roadmap is one screen plus the feature table. If
  the vision needs three paragraphs, the project is probably under-scoped for a
  roadmap pass — or it's really several projects.
- Don't pre-write requirements here. The roadmap says *what* features exist and
  *why they're ordered that way*; each feature's own `1-requirements.md` carries
  the detail.
- `specs/roadmap.md` is a top-level file, not an `NNN` directory — it never
  collides with feature numbering.
