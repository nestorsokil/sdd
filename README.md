# SDD Skill

A personal skill that tailors Spec-Driven Development to my workflow and
preferences. Not a generic SDD framework — opinions are baked in.

## What it does

Guides an agent through a structured spec-before-code workflow:

```
requirements.md  →  design.md  →  tasks.md  ║  (analyze + implementation: opt-in)
     ↑ approve        ↑ approve      ↑ approve
```

The **spec set is the deliverable.** Three short markdown files (requirements,
design, tasks) live in the repo, version-controlled alongside code, and serve
as both engineering-review artifacts and lightweight documentation after a
feature ships.

Implementation is a separate, explicit step — not an automatic continuation
of the spec flow. You review the spec, hand it to a colleague or a future
agent, then trigger implementation when ready.

## Workflow at a glance

0. **(Opt-in) Roadmap / breakdown** — split a high-level description (greenfield
   project or large brownfield change) into an ordered list of vertically-sliced,
   deployable features, captured as a living index in `specs/roadmap.md`. Each
   feature then runs the spec flow below.
1. **Requirements** — what the feature does and why; testable acceptance
   criteria with stable IDs (`AC-1`, `C-1`, `NG-1`).
2. **Design** — how it will be built; component boundaries, data flow,
   alternatives considered. A codebase-exploration agent and an architecture
   agent run in parallel before drafting.
3. **Tasks** — 5–15 ordered, file-scoped tasks, each pointing back to the
   spec sections it implements (drift detection touchpoint).
4. **Stop.** The spec is reviewed by humans.
5. **(Opt-in) Analyze** — cross-artifact consistency check: every requirement
   has a task, no constraint violations, no naming drift, no orphan tasks.
6. **(Opt-in) Implement** — one task per agent turn. Agent stops after each
   task. User reviews the git diff and commits manually. Drift between code
   and spec gets reconciled into the spec in the same diff.
7. **(Opt-in) Self-review** — after each task the agent offers to run a
   self-review suite (spec-conformance + correctness + security by default;
   clean-code / test-quality added when the diff warrants). Defer it and
   batch-review later with `/sdd review <name>`. Each task carries a `Review:`
   stamp (`pending` / `passed`) so deferred reviews are never lost.

Three steer types absorb mid-flight changes: **tweak** (apply inline),
**amend** (delta + re-approval), **pivot** (re-run phase). Re-opened
implemented specs use a dedicated `amending` status.

## How it differs from neighbors

| | This skill | superpowers | spec-kit |
|---|---|---|---|
| Output | requirements + design + tasks | brainstorm spec + plan | constitution + spec + plan + tasks |
| Code in tasks | pseudo-code, file paths | full TDD step blocks | structured |
| Per-step ceremony | low — one task per turn, no auto-commit | high — strict TDD red-green-refactor | medium |
| Implementation default | **opt-in** (spec set is the deliverable) | continues to execution | continues to implement |
| Status lifecycle | explicit (`draft / approved / implemented / amending`) | implicit | implicit |
| Mid-flight steering | classified (tweak / amend / pivot) | reactive | not formalized |
| Constitution / project rules | defers to `CLAUDE.md` | not formalized | first-class artifact |
| Bug fix flow | dedicated, **test-first** with exploratory-test fallback | same as feature flow | same as feature flow |
| Escape hatch | yes — skip SDD for <50 LOC trivial changes | no | no |

Sweet spot: solo or small-team work where the spec is itself a review
artifact, you want structure without TDD ceremony, and you commit by hand
after reviewing each task.

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill instructions — loaded by the agent when SDD is triggered |
| `AGENT-SNIPPET.md` | Paste into a project's agent instructions file (AGENTS.md, GEMINI.md, etc.) to enable SDD for non-Claude-Code agents |
| `references/roadmap-template.md` | Template for the project decomposition / roadmap phase |
| `references/requirements-template.md` | Template for the requirements phase |
| `references/design-template.md` | Template for the design phase |
| `references/tasks-template.md` | Template for the task breakdown phase |
| `references/bugfix-template.md` | Template for the test-first bug fix flow |
| `references/research-template.md` | Template for the optional research doc |

## Usage

In Claude Code, trigger naturally ("let's spec this out") or explicitly with
`/sdd <subcommand>`:

```
/sdd roadmap <project> split a project into vertically-sliced features (alias: breakdown)
/sdd spec <name>       requirements → design → tasks (stops at tasks approval)
/sdd design <name>     skip requirements, start from design
/sdd tasks <name>      skip to task breakdown
/sdd bugfix <name>     test-first bug fix flow
/sdd implement <name>  build against an approved spec set, one task per turn
/sdd review <name>     run self-review over tasks with deferred reviews
/sdd resume <name>     continue from existing specs
```

For other agents, paste `AGENT-SNIPPET.md` into the project's instructions file.
