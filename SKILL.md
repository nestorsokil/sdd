---
name: sdd
description: >
  Spec-Driven Development workflow — a lightweight, phased approach to building features
  through structured specs before writing code. Use this skill whenever the user says
  "spec", "sdd", "design first", "write a spec", "spec-driven", or asks to plan a feature
  before implementing it. Also trigger when the user says things like "let's think through
  this before coding", "I want to design this properly", "break this down into phases",
  or references requirements/design/tasks documents for a feature. This skill produces
  markdown spec files (requirements, design, tasks) that live in the repo alongside code
  and act as durable context for implementation. Do NOT trigger for one-off
  questions, quick bug fixes the user wants done immediately, or when the user just wants
  code written without planning.
---

# Spec-Driven Development (SDD)

A lightweight, phased workflow for building features through structured specs before code.
The specs are plain markdown files that live in the repo — they serve as durable context
across sessions and are version-controlled alongside code. They should also be usable as human-readable documentation of features.

## Why this exists

Jumping straight to code produces working-but-wrong solutions — code that compiles
but misses edge cases, violates constraints, or solves the wrong problem. SDD forces
a think-then-build cadence: each phase produces a short markdown artifact the human
reviews before the next phase begins.

## When NOT to use SDD

Before invoking the workflow, sanity-check the scope. SDD is overhead for
genuinely small changes. If all of the following are true, skip SDD and just
do the work:

- Change fits in <50 LOC and one or two files.
- No new behavior — only a typo, config bump, log line, formatting, or
  rename.
- No new contract (no API change, no schema change, no new dependency).
- A test failure or commit message would be enough context for a future
  reader.

When in doubt, ask: "this looks small — do you want me to just do it, or run
SDD?" Don't unilaterally generate a 3-doc spec for a one-line fix.

## The three phases

```
1-requirements.md  →  2-design.md  →  3-tasks.md  ║  (analyze + implementation: opt-in)
       ↑ approve          ↑ approve       ↑ approve
```

**The spec set is the deliverable.** Once 3-tasks.md is approved, the workflow
stops. The three docs are an artifact for engineering review — a colleague,
a reviewer, or a future implementer reads them and decides what's next.

Do NOT proceed to implementation automatically. Implementation only starts
when the user explicitly says "let's implement", "start building", "do task
1", or similar. At that point, run the cross-artifact analyze step (Phase 3.5)
and then the implementation phase as described below.

Every phase ends with an explicit approval gate. Do NOT advance to the next phase
until the user says "approved", "lgtm", "go", "next", "y", or similar affirmative.

If the user asks to skip a phase (e.g., "skip requirements, I know what I want"), that's
fine — acknowledge which phase you're skipping and proceed to the next one.

## File layout

```
specs/
  <NNN>-<feature-name>/
    1-requirements.md
    2-design.md
    3-tasks.md
    research.md          (optional — only when design alternatives were discussed)
    diagrams/            (optional — drawio files + exported images)
      *.drawio
      *.png / *.svg
```

`<NNN>` is a zero-padded 3-digit sequence number (`001`, `002`, …). When creating
a new spec set, scan `specs/` for the highest existing `<NNN>` and increment by one.
If `specs/` is empty or doesn't exist, start at `001`.

If the project already has a different convention (e.g., `docs/specs/`, `design/`), follow
the existing convention. Ask if unclear.

---

## General principles

- **Readable, not exhaustive**: specs are read by humans. Short paragraphs, bullet lists,
  clear headings. If a section is turning into a wall of text, restructure it.
- **Pseudo-code over code**: describe logic in plain language or pseudo-code. Use full
  code snippets only when ambiguity would remain without them.
- **Ask, don't assume**: during requirements and design phases, ask clarifying questions
  when the intent is ambiguous, a constraint is unstated, or multiple valid interpretations
  exist. Don't guess at business rules, user expectations, or integration behavior — surface
  the question and let the user decide. Skip questions with obvious answers or that don't
  change the shape of the solution.
- **Interactive creation**: surface high-level options and let the user steer. Don't
  silently decide on meaningful trade-offs — present the choice and your recommendation,
  then wait for direction.
- **Spec status**: every spec file carries a status line (`> Status: draft`). Update it
  as the work progresses:
  - `draft` — being written for the first time
  - `approved` — user has signed off; do not edit without re-opening
  - `implemented` — all tasks complete; spec is now reference documentation
  - `amending` — previously `implemented`, now being changed for a follow-up.
    Use this instead of dropping back to `draft`, so the history "this was
    shipped once" stays visible. Treat the change as an amendment per the
    steering rules; status returns to `approved` after sign-off, then
    `implemented` once the new tasks ship.
- **Spec freshness**: if during implementation the code diverges from the spec —
  whether the user asked for a steer, you discovered a design flaw, or you
  noticed drift after editing a file — pause and reconcile. Either the spec was
  wrong (update it via the steering rules below) or the code is wrong (revert
  it). Never let them drift silently. The spec is the source of truth; stale
  specs are worse than no specs.

---

## Phase 1: Requirements (`1-requirements.md`)

Goal: capture *what* the feature does and *why*.

Read the template: `./references/requirements-template.md`

Key principles:
- Before writing, ask clarifying questions if the user's description is vague or leaves
  meaningful gaps — who uses this, what triggers it, what happens on failure, what are
  the boundaries. Don't draft a requirements doc built on guesses; get the answers first.
  But don't interrogate — if the answer is obvious from context, just use it.
- Acceptance criteria should be testable assertions, not vague descriptions.
  Bad: "The system should be fast." Good: "P95 latency < 200ms for single-item lookup."
- List constraints and non-goals explicitly. Constraints prevent over-engineering.
  Non-goals prevent scope creep.
- Keep it short. A typical requirements doc is 30-60 lines. Past 80 lines, the feature
  is probably too big — suggest splitting.
- Don't pad with obvious statements. A simple feature gets a simple doc.

After writing, present the doc and wait for approval. On approval, set status to `approved`.

## Phase 2: Design (`2-design.md`)

Goal: capture *how* the feature will be built — components, interfaces, data flow.

Read the template: `./references/design-template.md`

Key principles:
- Before drafting, run two agents in parallel:
  1. A codebase exploration agent — map the modules and interfaces the feature will
     touch, existing patterns, naming conventions, and any existing specs that overlap.
     Report back: affected files, relevant patterns, existing interfaces to reuse.
  2. An architecture design and review agent — given the requirements doc and a brief
     description of the feature, propose the key design decisions (components,
     data ownership, communication pattern, failure modes) and flag any architectural
     concerns before a full draft is written.
  Synthesize both outputs into 2-design.md. This ensures the design presented at the
  approval gate is already architecture-reviewed, not just drafted.
- If no suitable agents are available, fall back to exploring the codebase sequentially
  before drafting, then self-review for architectural issues.
- **Greenfield**: if the project is empty or the feature touches no existing code
  (new module, new service, new repo), skip the codebase-exploration agent —
  there is nothing to explore. Run only the architecture agent.
- Ask clarifying questions when the design has ambiguous integration points, unclear
  data ownership, or multiple reasonable approaches. Don't assume how an upstream service
  behaves or what a consumer expects — ask. Skip questions where the codebase or
  requirements doc already provides a clear answer.
- Every design decision should trace back to a requirement or constraint.
- Describe component boundaries and interfaces, not internal implementation details.
- Include a data flow section. This catches integration issues early.
- For diagrams: use Mermaid inline (sequence, flowchart, entity). For complex
  visualizations that would be cramped in Mermaid, place a `.drawio` file and an
  exported `.png`/`.svg` in `diagrams/` and reference the image from the doc.
- Include a brief metrics section listing new or changed metrics (name + what it tracks).
  One line each — no alerting thresholds or implementation detail.
- Name the alternatives you considered and why you rejected them. If significant
  exploration happened during spec creation, capture it in `research.md` instead of
  bloating this doc — but keep a brief summary here so 2-design.md stays self-contained.
- If the feature touches persistence, include schema. If it touches APIs, include
  request/response shapes.

After writing, present the doc and wait for approval. On approval, set status to `approved`.

## research.md (optional)

Create this file when competing design approaches were actively discussed during spec
creation. It captures the full exploration: options, trade-offs, and why the chosen
direction won.

Do NOT create it for routine decisions. Only when real back-and-forth happened and the
fuller context is worth preserving as documentation.

## Phase 3: Tasks (`3-tasks.md`)

Goal: an ordered checklist of implementation steps, each small enough to complete
in one focused session.

Read the template: `./references/tasks-template.md`

Key principles:
- Each task should be independently verifiable — after completing it, something
  observable changed (a test passes, an endpoint responds, a log line appears).
- Include file paths. This makes each task actionable without re-reading the design.
- Order so each builds on the previous. Foundation first, then logic, then wiring,
  then tests.
- Mark complexity: `[small]`, `[medium]`, `[large]` to guide review cadence.
- 5-15 tasks per feature. Fewer = too coarse. More = split the feature.

After writing, present the doc and wait for approval. On approval, set status
to `approved`. **This is the end of the default workflow.** The spec set is now
ready for engineering review. Do not begin implementation unless the user
explicitly asks for it.

## Phase 3.5: Cross-artifact analyze (after tasks, before implementation)

Once 3-tasks.md is approved, run a single consistency check across requirements,
design, and tasks **before** any code is written. This is the last cheap moment
to catch coverage gaps and contradictions; finding them mid-implementation
costs a rewrite.

What to check:
- **Requirement coverage**: every acceptance criterion in 1-requirements.md is
  satisfied by at least one task. List uncovered criteria.
- **Constraint compliance**: every constraint and non-goal in 1-requirements.md
  is respected by design and tasks. Flag violations
  (e.g. "no new dependencies" + a task that adds one; "must run in <200ms" +
  no perf-sensitive design choice).
- **Invariant testability**: every invariant declared in 2-design.md (data
  ownership, ordering, idempotency, auth boundaries, failure modes) is either
  covered by an existing test or by a task that adds one.
- **Naming consistency**: function names, field names, endpoint paths, type
  names, metric names match across 2-design.md and 3-tasks.md. A method called
  `clearLayers()` in design but `clearFullLayers()` in tasks is a bug.
- **Task traceability**: every task points back to a requirement or design
  element. Tasks with no parent are scope creep — flag for removal or
  justification.
- **Placeholder scan**: any "TBD", "TODO", "fill in later", or vague stubs
  in any of the three docs.

Output a short report with sections:
- `Covered` — one line summary
- `Gaps` — uncovered requirements / untested invariants
- `Contradictions` — name mismatches, conflicting statements
- `Constraint violations` — design/tasks that break a stated constraint
- `Orphan tasks` — tasks with no parent in requirements/design
- `Placeholders` — any unresolved stubs

Present the report to the user.

- If everything is `Covered` and no other section has entries, proceed to
  implementation.
- If anything appears in `Gaps`, `Contradictions`, `Constraint violations`,
  or `Placeholders`: set the status of every affected doc back to `draft`,
  fix the issue, and re-approve before implementation.
- `Orphan tasks` should be discussed with the user — either drop them, or
  amend 1-requirements.md / 2-design.md to legitimize them (then re-approve).

This is a small, fast check — not a full re-review. Do not re-litigate
approved decisions.

## Implementation phase

Once the analyze report is clean and 3-tasks.md is still approved, before writing
any code, spawn a clean code and design review agent on the files the feature
will touch most. Review findings with the user — surface existing issues in
the landing zone before building on top of them. Skip this step if the files
were recently reviewed or the feature is greenfield.

**Default execution model: one task per agent turn.**

The agent implements exactly one task, returns control, and stops. The user
reviews the git diff, commits manually, then asks for the next task — possibly
after `/compact` or in a fresh agent window. This keeps each task reviewable
in isolation, keeps context lean, and puts the human in the loop for each commit.

The agent does NOT auto-continue, does NOT stage files, does NOT create commits.
Any steering, drift findings, or amendments must land in the relevant spec doc
(1-requirements.md / 2-design.md / 3-tasks.md) — not in a chat report. The user will
review the spec edits in the same git diff as the code.

For each task:

1. State which task you're starting (by number and title).
2. Implement it.
3. Run unit tests relevant to the change. Unit tests are fast and cheap — run
   them after every task, not just at the end.
4. **Drift scan**: compare the diff against the spec sections listed in the
   task's "Spec touchpoints" line. If anything in the code diverges, apply the
   steering rules below and update the affected spec section *in the same diff*.
   Do NOT check the task off until the spec and code agree.
5. Check it off in 3-tasks.md: `- [x] Task description`. (Markdown edit, not a
   git commit — the user commits everything together.)
6. State which task is next, then STOP. Do not start it.

### Auto-continue (opt-in only)

If the user explicitly asks to "auto-continue", "run all tasks", "go through
the whole thing", or similar — only then chain tasks without stopping. Even in
auto-continue, pause for confirmation on any tweak/amend/pivot, and never
auto-commit.

### Resuming in a fresh window

After `/compact` or in a new agent window, treat the next task as a cold start.
Read the spec files, the task's "Spec touchpoints", and the relevant existing
code before doing anything. The `resume <name>` subcommand handles this; the
user can also just say "next task" once the spec context is loaded.

**Testing guidance:**
- **Unit tests**: run after every task. If the task changes logic, there should be a
  unit test covering it — either existing or newly written.
- **Integration / performance / property tests**: these are typically heavier (require
  containers, external services, long runtimes). Consult the project's test setup to
  understand how easy they are to run. In most cases, suggest running them manually
  to the user rather than triggering them automatically. At the end of implementation,
  remind the user which heavier test suites should be run.
- **Integration test coverage**: before implementation begins, check whether existing
  integration tests can be easily extended to cover the new feature. If the feature
  requires significant new test infrastructure (new fixtures, containers, test harnesses),
  flag this early — include it in the task breakdown and consult with the user on scope.

When the last task is checked off, run the **post-implementation review suite**
in parallel on all files changed during implementation. The default suite:

1. **Clean code review** — naming, structure, abstractions, dead code.
2. **Security review** — injection, authz, secrets, input validation, common
   OWASP issues.
3. **Spec-conformance review** — reads 1-requirements.md + 2-design.md + the diff.
   Verifies every acceptance criterion is satisfied, the implementation matches
   the design (component boundaries, interfaces, data flow), and no constraint
   or non-goal is violated. Findings here default to **High** — building the
   wrong thing is the worst class of bug.
4. **Test-quality review** — examines tests added or modified during
   implementation. Flags: tautological tests (mock returns X, assert X),
   missing edge cases (empty, null, boundary, error paths, concurrency),
   tests that assert "no exception" instead of actual behavior, over-mocked
   tests that no longer prove anything about real integration.
5. **Correctness review** — narrow scope (no overlap with clean code):
   off-by-one, null/empty handling, error propagation, race conditions,
   resource leaks, incorrect assumptions about library behavior.

Optional, run when relevant:
- **Performance review** — only if 1-requirements.md states perf criteria
  (latency, throughput, memory). Otherwise skip; speculative perf review is noise.
- **Docs/README sync** — quick check whether business-logic changes require a
  README or external doc update. One pass, not a full agent.

Use the user's project-specific agents where defined (e.g. `clean-code-reviewer`,
`security-reviewer`). For reviews without a dedicated agent, dispatch the
generic `Agent` tool with an inline prompt scoped to that review's concern.

Run all applicable reviews in parallel — single message, multiple Agent calls.
Synthesize findings into one report grouped by severity (Critical / High /
Medium / Low), then present to the user.

Set status to `implemented` in 1-requirements.md, 2-design.md, and 3-tasks.md only
if there are no High or Critical findings — or if the user explicitly accepts
outstanding findings. The specs are then reference documentation.

If during implementation you discover the design is wrong or incomplete, STOP.
Use the steering rules below to classify the change and route it correctly.
Catching design issues during implementation is exactly why the specs exist.

## Steering during implementation

The spec is not frozen at approval. Users will steer mid-flight — small renames,
new requirements, scope shifts, or full pivots. The job is to absorb the steer,
keep the spec in sync with the code, and not let drift accumulate.

Classify every steer into one of three types and announce which mode you're using.
The user can override the classification if you got it wrong.

### Type 1: Tweak (small, local, no contract change)

Examples: rename a variable or field, swap a library, change a default value,
drop a sub-task, reorder steps within a task, fix a typo in the spec.

**Process:**
1. Apply the change to the code (or note where it will apply in upcoming tasks).
2. Update the affected spec section inline.
3. Status stays `approved`. No re-approval gate.
4. Briefly note the edit: "Tweak applied: <what>. Updated <spec section>."
5. Continue.

### Type 2: Amendment (changes contract or scope, but localized)

Examples: new acceptance criterion, new endpoint field, changed error code,
new constraint, additional task, changed data ownership between two existing
components.

**Process:**
1. Stop current task if mid-implementation.
2. Set the affected spec doc(s) back to `draft`.
3. Draft the delta — show the *diff* of what's changing in 1-requirements.md,
   2-design.md, and/or 3-tasks.md, not a full rewrite.
4. Present and wait for approval.
5. On approval, set affected docs back to `approved`.
6. Re-run the cross-artifact analyze step on touched docs (Phase 3.5) — small
   amendments often introduce coverage gaps or naming inconsistencies.
7. Resume implementation.

### Type 3: Pivot (invalidates approved direction)

Examples: whole new architectural approach, dropped feature, fundamental
constraint added (e.g. "this needs to work offline now"), reversed assumption.

**Process:**
1. Stop. Do not continue current task.
2. Set affected docs to `draft`.
3. Re-run the relevant phase from the top (re-design, re-tasks, possibly
   re-requirements). Don't try to patch — the approved direction is gone.
4. Identify which already-completed tasks are now invalidated. For each:
   propose either an unwind task or a follow-up task to bring code in line
   with the new direction. Surface this explicitly to the user.
5. Get re-approval per phase as usual.
6. Re-run cross-artifact analyze before resuming.

### Explicit steering keywords

The user can prefix a steer to skip classification:
- `tweak: <change>` → Type 1
- `amend: <change>` → Type 2
- `pivot: <change>` → Type 3
- `update spec` → re-derive the affected spec sections from the current code
  state (use when the agent missed drift or after a manual edit by the user)

Without keywords, classify and announce ("Treating this as an amendment because
it adds a new acceptance criterion. Drafting delta now."). User corrects if wrong.

### Drift detection per task

After completing each task, before checking it off, do a quick drift scan:
look at the diff against the spec sections this task was supposed to implement.
If anything in the code diverges from the spec (renamed field, different return
shape, skipped a constraint), surface it as a tweak or amendment before moving
on. Cheap when done per task; expensive at the end.

### Stale checked-off tasks

If a tweak or amendment invalidates a task that is already checked off (e.g. a
field rename affects a task that already shipped), do not silently re-edit
history. Either:
- Add a follow-up task that brings the shipped code in line with the change, or
- Add a sub-step to the next task that touches the same area.

Either way, the change must appear in 3-tasks.md so the trail is preservable.

---

## Entry points

When invoked as `/sdd $ARGUMENTS` or via natural language, route based on the first word:

| Subcommand | Behavior |
|------------|----------|
| `spec <name>` | Full 3-phase spec flow (1-requirements.md → 2-design.md → 3-tasks.md). **Stops at 3-tasks.md approval.** Spec set is the deliverable. |
| `design <name>` | Skip requirements, start from 2-design.md. Stops at 3-tasks.md approval. |
| `tasks <name>` | Skip to task breakdown (design exists or is trivial). Stops at 3-tasks.md approval. |
| `bugfix <name>` | Abbreviated, **test-first** flow using `./references/bugfix-template.md` — root cause → reproduction → failing regression test → fix approach → tasks. If the user can't describe repro steps, propose exploratory tests from code-reading hypotheses. No requirements phase. |
| `implement <name>` | Begin implementation against an approved spec set. Runs Phase 3.5 (analyze) then implements one task per turn. Use this when the spec is reviewed and ready to build. |
| `resume <name>` | Read existing specs from `specs/<NNN>-<name>/`, determine current state, and continue (see below). Also accepts just `resume <NNN>` — search for the directory matching that number. |

**Numbering new specs:** Before creating `specs/<NNN>-<name>/`, run `ls specs/` (or the
project's equivalent path) to find the highest existing `<NNN>`, then increment by one.
If `specs/` doesn't exist yet, create it and start at `001`.

**Resuming (`resume <name>`):** Search `specs/` for a directory matching the name or
number. Read all spec files inside. Determine the current state by checking:
1. Which spec files exist and their `> Status:` line.
2. If 3-tasks.md exists and is approved, how many tasks are checked off.
3. If any spec is in `draft` status, it was being worked on or needs re-approval.

Present a brief summary of where things stand ("1-requirements.md approved, 2-design.md approved,
3 of 8 tasks complete — next up is task 4: ...") and confirm with the user before
continuing.

If invoked with no arguments, ask the user which flow they want to start.

For `research.md`, read `./references/research-template.md` before writing.

For diagrams: commit both the `.drawio` source (for editing) and the exported
`.png`/`.svg` (for easy review in pull requests).

---

## Adapting the workflow

The templates are defaults. The user might customize:
- Add an `adr.md` phase for architectural decisions.
- Add a `migration.md` phase for data migrations.
- Drop user-story format for solo-developer projects.
- Change the spec path convention.

The workflow is the value, not the templates.
