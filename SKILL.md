---
name: sdd
description: >
  Spec-Driven Development workflow — a lightweight, phased approach to building features
  through structured specs before writing code. Use this skill whenever the user says
  "spec", "sdd", "design first", "write a spec", "spec-driven", or asks to plan a feature
  before implementing it. Also trigger when the user says things like "let's think through
  this before coding", "I want to design this properly", "break this down into phases",
  or references requirements/design/tasks documents for a feature. Trigger the roadmap
  flow when the user says "roadmap", "breakdown", "break this into features", "split the
  project", or describes a large greenfield/brownfield effort needing decomposition before
  any single spec. This skill produces
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

- Change fits in <200 LOC and 2-3 files.
- No new behavior — only a typo, config bump, log line, formatting, or
  rename.
- No new contract (no API change, no schema change, no new dependency).
- A test failure or commit message would be enough context for a future
  reader.
- Time-sensitive: when user signals this needs a quick update.

When in doubt, ask: "this looks small — do you want me to just do it, or run
SDD?" Don't unilaterally generate a 3-doc spec for a one-line fix.

## The three phases

```
1-requirements.md  →  2-design.md  →  3-tasks.md  →  ║ approve set ║  (analyze + implementation: opt-in)
```

The three docs are produced in one pass, then reviewed together. The agent writes
all three back-to-back — asking clarifying questions *during* creation as needed
(those are inputs, not gates) — then presents the complete set and waits for
steering or approval before anything is implemented.

**The spec set is the deliverable.** Once the set is approved, the workflow stops.
The three docs are an artifact for engineering review — a colleague, a reviewer,
or a future implementer reads them and decides what's next.

Do NOT proceed to implementation automatically. Implementation only starts
when the user explicitly says "let's implement", "start building", "do task
1", or similar. At that point, run the cross-artifact analyze step (Phase 3.5)
and then the implementation phase as described below.

**Single approval gate.** Produce all three docs without stopping for approval
between them, each written as `> Status: draft`. Then present the set as a whole
and wait. The user steers — tweaks or amendments to any of the three (see Steering)
— or approves with "approved", "lgtm", "go", "y", or similar. On approval, flip
all three to `approved`.

Why one gate instead of three: per-phase approval burns round-trips. Producing the
full set first lets the user review requirements, design, and tasks together, in
context, and steer in a single pass. The trade-off: a steer on requirements can
ripple into design and tasks — when it does, reconcile the whole set, don't patch
one doc in isolation.

If the user prefers phase-by-phase review ("stop after requirements", "let me
approve each phase"), honor that and gate each phase instead.

If the user asks to skip a phase (e.g., "skip requirements, I know what I want"), that's
fine — acknowledge which phase you're skipping and proceed.

## File layout

```
specs/
  roadmap.md             (optional — greenfield/multi-feature decomposition; living index)
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
  - `reviewing` — all tasks complete; self-review suite running or findings being
    resolved. Transitions to `implemented` once review is clean and user approves.
  - `implemented` — all tasks complete, review clean, user approved.
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

## Phase 0: Roadmap (`specs/roadmap.md`, project decomposition)

Goal: turn a high-level description into an ordered list of feature-sized
chunks, each of which then feeds the per-feature spec flow.

**When to use it:** any large multi-feature effort described only at a high
level, where the feature boundaries aren't obvious yet — a greenfield project
*or* a large change to an existing (brownfield) codebase. The user has "here's
the whole thing I want to build/change" and needs it split before any single
`spec <name>` makes sense.

**When NOT to use it:** a single, well-scoped feature — go straight to `spec`.
A roadmap for one feature is overhead. Also skip it when the *whole* effort, even
if loosely described, turns out small enough to ship as one increment: don't
manufacture multiple features out of a small scope. Gauge the full scope first
(step 2 below) and only decompose if there are genuinely separate, sequenceable
features.

Read the template: `./references/roadmap-template.md`

**Process:**
1. Clarify project-level intent first — vision, primary users, hard constraints,
   the MVP boundary, and anything explicitly out of scope. Same ask-don't-assume
   discipline as Phase 1, but at project granularity. Don't decompose on guesses.
2. **Gauge the full scope before splitting anything.** Once intent is clear, judge
   whether the effort genuinely warrants decomposition. If the whole thing is small
   enough to land as a single deployable increment — roughly one cohesive spec's
   worth of work, no independent sub-features worth sequencing — do NOT produce a
   roadmap. Say so plainly ("this is small enough for one feature — skipping the
   roadmap") and route straight to `spec <name>` with the whole scope as that one
   feature. A roadmap that lists a single feature is pure overhead. Only decompose
   when there are real, separately-shippable features to sequence.
3. Understand the landscape, then propose a decomposition:
   - **Brownfield** — run a codebase-exploration agent *and* an architecture
     agent in parallel (same pairing as Phase 2). The exploration agent maps the
     modules, seams, and existing patterns the effort will touch; the
     architecture agent uses that to propose feature boundaries and ordering.
   - **Greenfield** — skip codebase exploration (nothing to explore); run only
     the architecture agent.
   In both cases the architecture agent proposes boundaries, dependency ordering,
   and which foundational/cross-cutting work (data model, auth, shared infra)
   comes first.
4. **Slice features vertically.** Each feature should be a deployable increment —
   a thin path through the stack that delivers user-observable value on its own,
   not a horizontal layer ("all the DB work", "all the API work") that ships
   nothing until a sibling lands. Prefer features that leave the system shippable
   when complete. Foundational/cross-cutting work that genuinely can't be sliced
   into a vertical (shared schema, auth substrate) is allowed as an early feature,
   but call it out as such rather than defaulting to layer-by-layer splits.
   Order features so each lands a runnable increment: get the first feature's
   happy path working and verifiable, then move to the next, rather than building
   broad foundations across many features before any one runs. Within each
   feature, the same happy-path-first cadence applies at task level (see Phase 3).
5. Present the proposed feature list with your recommendation. This is
   interactive: surface the trade-offs of each boundary and let the user
   merge, split, or reorder. Don't silently decide the cut points.
6. On approval, write `specs/roadmap.md` from the template — assign `NNN` in
   dependency order, every feature `planned`. Set the roadmap `Status: approved`.
7. Hand off: suggest `/sdd spec <first-feature>` and note that `specs/roadmap.md`
   is now the living project index.

Like every phase, this ends with an explicit approval gate before the file is
written as `approved`.

**Integration with `spec <name>`:** when `specs/roadmap.md` exists, the spec flow
should lean on it instead of re-asking:
- Take the feature's `NNN`, one-line purpose, and the project constraints
  (`PC-*`) from the roadmap row — seed `1-requirements.md` with them rather than
  re-deriving the number or re-asking project-wide constraints.
- Flip the roadmap row's status to `specced` once the feature's spec set reaches
  `approved`, and to `implemented` once its tasks are all `implemented`.

The roadmap owns `NNN` assignment once it exists: `spec` reuses the roadmap's
number for that feature instead of re-scanning `specs/`. `specs/roadmap.md` is a
top-level file, not an `NNN` directory, so it never collides with feature
numbering.

## Phase 1: Requirements (`1-requirements.md`)

Goal: capture *what* the feature does and *why*.

Read the template: `./references/requirements-template.md`

Key principles:
- Ask clarifying questions whenever ambiguity arises — before writing, or mid-draft
  if something unclear surfaces. Don't guess at business rules or edge cases; ask and
  wait for an answer. Do NOT park unresolved questions in the doc as an "Open questions"
  section — there is no such section. Clarify interactively, then write.
  Don't interrogate — if the answer is obvious from context, just use it.
- Acceptance criteria should be testable assertions, not vague descriptions.
  Bad: "The system should be fast." Good: "P95 latency < 200ms for single-item lookup."
- List constraints and non-goals explicitly. Constraints prevent over-engineering.
  Non-goals prevent scope creep.
- Keep it short. A typical requirements doc is 30-60 lines. Past 80 lines, the feature
  is probably too big — suggest splitting.
- Don't pad with obvious statements. A simple feature gets a simple doc.

Write it as `draft`, then continue straight to Phase 2 — do not stop for approval
here (unless the user asked to gate each phase). Approval happens once, on the full
set.

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
- Describe the data flow. For a linear, obvious flow, a few sentences or a short
  list is enough — do not draw a diagram for it.
- **Diagrams only earn their place when the logic is complex or a dependency/flow
  is non-obvious** — branching/concurrent sequences, multi-component interactions,
  state machines, or relationships that prose can't make clear. A diagram for a
  simple request→service→DB→response path is noise; describe it in one line instead.
  When a diagram is warranted, use Mermaid inline (sequence, flowchart, entity).
  For complex visualizations that would be cramped in Mermaid, place a `.drawio`
  file and an exported `.png`/`.svg` in `diagrams/` and reference the image from the doc.
- Include a brief metrics section listing new or changed metrics (name + what it tracks).
  One line each — no alerting thresholds or implementation detail.
- Name the alternatives you considered and why you rejected them. If significant
  exploration happened during spec creation, capture it in `research.md` instead of
  bloating this doc — but keep a brief summary here so 2-design.md stays self-contained.
- If the feature touches persistence, include schema. If it touches APIs, include
  request/response shapes.
- **Keep it short — this is a lightweight workflow.** A typical design doc is
  40-80 lines. The point of SDD here is to be lighter than heavyweight design
  processes, not to produce an exhaustive spec. Capture the decisions and
  boundaries a reviewer needs; omit anything they could infer from the code.
  Skip sections that don't apply rather than filling them with "N/A" prose. If a
  doc is ballooning past ~100 lines, the feature is probably too big — suggest
  splitting it.

Write it as `draft`, then continue straight to Phase 3 — do not stop for approval
here (unless the user asked to gate each phase).

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
- Each task should leave the repo in a working, tested state — it builds, all
  pre-existing tests pass, and any new logic it adds is covered by a unit test in
  the same change. Enforced by the implementation completion gate. Unit tests
  ride *with* their task; do not split "write the tests" into a trailing task.
- Each task should be independently verifiable — after completing it, something
  observable changed (a test passes, an endpoint responds, a log line appears).
- Include file paths. This makes each task actionable without re-reading the design.
- **Order for the fastest runnable increment — happy path first (walking
  skeleton).** Build the thinnest end-to-end slice that makes one real path run
  and be verifiable, then layer edge cases, error handling, and validation as
  follow-on tasks. Build only the foundation the happy path needs — do NOT
  front-load all infrastructure before anything runs. The cadence is: thin
  happy-path slice → tests that make it runnable/verifiable → edge cases → tests
  for those → next slice. Each task still builds on the previous, but the goal is
  a working increment early, not a complete foundation before any behavior works.
- **Happy-path verification can come early.** Unit tests still ride with each
  task (the completion gate). On top of that, a smoke or integration test proving
  the happy-path slice actually runs end-to-end is worth scheduling *right after*
  that slice — not deferred to a trailing task — so the increment is provably
  runnable before edge cases pile on. Heavier/exhaustive integration tests still
  default to a dedicated task near the end (see implementation Testing guidance).
- Mark complexity: `[small]`, `[medium]`, `[large]` to guide review cadence.
- 5-15 tasks per feature. Fewer = too coarse. More = split the feature.

After writing this doc, all three are done. **Now present the full set —
1-requirements.md, 2-design.md, 3-tasks.md — and wait for steering or approval.**
On approval, flip all three to `approved`. **This is the end of the default
workflow.** The spec set is now ready for engineering review. Do not begin
implementation unless the user explicitly asks for it.

If the user steers on the set (e.g. "tighten AC-2", "the design should use a
queue", "drop task 5"), treat it per the Steering rules — and remember a change
to an earlier doc can ripple forward, so reconcile design and tasks too before
re-presenting.

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

Once the analyze report is clean and 3-tasks.md is still approved, **before writing
any code**, ask the user two things:

**1. Git branch** — check whether a feature branch already exists (run `git branch`
and `git status` to see the current branch). If the user is already on a dedicated
feature branch, note it and continue. If they're on `main`/`master` or a shared
branch, suggest creating a feature branch now. Let the user decide — they may prefer
to work on main. Do not create a branch without their explicit go-ahead.

**2. Execution mode** — ask the user which cadence they want:

| Mode | Behavior |
|------|----------|
| **Pause** (default) | Implement one task, stop. User reviews the diff, then asks for the next task. |
| **All-in-one** | Implement all tasks back-to-back without stopping. One large diff at the end. |
| **Auto-commit** | Implement one task, create a git commit for it, then continue to the next. User reviews commits individually after. |

Wait for their answer before proceeding. If they don't express a preference, use **Pause**.

Once branch and mode are settled, **offer** to spawn a clean code and design review
agent on the files the feature will touch most — surfacing existing issues in the
landing zone before building on top of them. This is opt-in: skip by default if the
files were recently reviewed or the feature is greenfield.

**Execution model details:**

In **Pause** mode: the agent implements exactly one task, returns control, and stops.
The user reviews the git diff, commits manually if they want, then asks for the next
task — possibly after `/compact` or in a fresh agent window. The agent does NOT stage
files or create commits.

In **All-in-one** mode: the agent implements all tasks without stopping. Completion
gate and drift scan still run per task internally, but control is not returned until
the last task is done. The agent does NOT create commits.

In **Auto-commit** mode: after each task passes its completion gate and drift scan,
the agent creates a git commit scoped to that task, then continues to the next. Commit
message references the task title and number. The user reviews the git log after.

In all modes: any steering, drift findings, or amendments land in the relevant spec
doc (1-requirements.md / 2-design.md / 3-tasks.md) — not in a chat report. The user
reviews spec edits in the same diff/commit as the code.

For each task:

1. State which task you're starting (by number and title).
2. Implement it. Keep the diff scoped to this task only — no opportunistic
   refactors, reformatting, or unrelated changes. A task's diff should be one
   self-contained, reviewable unit; mixed diffs are the main thing that slows
   human review.
3. **Completion gate** — the task is not done until all three hold. If any
   fails, fix it before checking off (do not defer to a later task):
   - **Build passes**: the project compiles / type-checks. A task that leaves
     the repo unbuildable is not complete.
   - **Pre-existing tests stay green**: run the unit tests relevant to the
     change (fast and cheap — run after every task, not just at the end). No
     task may leave a previously-passing test broken. A genuine regression
     halts the loop — see [Regressions](#testing-guidance) below.
   - **New logic is covered**: any new behavior this task introduces has at
     least one unit test *in this same diff* — either new or an extended
     existing test. A task that adds logic without a test is not done. (Pure
     wiring/config tasks with no new logic are exempt; say so explicitly.)
   This gate keeps the repo in a working, tested state after every task, so any
   checked-off task is a safe stopping point.
4. **Drift scan**: compare the diff against the task's "Spec touchpoints" and
   reconcile any divergence per Steering → Drift detection (below) before
   checking off — update the affected spec section *in the same diff*. Do NOT
   check the task off until the spec and code agree.
5. Check it off in 3-tasks.md: `- [x] Task description`. (Markdown edit, not a
   git commit — the user commits everything together.)
6. **Self-review**:
   - **Pause mode**: offer to run the self-review suite on this task's diff now,
     or defer. Record the outcome in the task's `Review:` stamp (`passed` /
     `pending`).
   - **All-in-one / Auto-commit mode**: skip the per-task offer — mark every task
     `Review: pending` and run the full batch review automatically once the last
     task is done (see Finishing up).
7. In **Pause** mode: state which task is next, then STOP. Do not start it.
   In **All-in-one** or **Auto-commit** mode: proceed to the next task automatically.
   In all modes: pause for confirmation on any tweak/amend/pivot.

### Resuming in a fresh window

After `/compact` or in a new agent window, treat the next task as a cold start.
Read the spec files, the task's "Spec touchpoints", and the relevant existing
code before doing anything. The `resume <name>` subcommand handles this; the
user can also just say "next task" once the spec context is loaded.

**Testing guidance:** <a name="testing-guidance"></a>
- **Unit tests**: enforced by the per-task completion gate (step 3) — new logic
  ships with a covering test in the same diff, and pre-existing tests stay green.
- **Regressions**: if the completion gate surfaces a broken pre-existing test,
  first decide whether it's a direct consequence of this task's change or an
  unrelated regression. For an unrelated regression, explain it and STOP for
  user input before proceeding — do not paper over it to make the gate pass.
- **Integration tests — default to a separate task.** Unit-level coverage rides
  with each implementation task (the gate); integration tests are typically
  heavier (containers, external services, long runtimes) and are best batched
  into their own dedicated task near the end of the breakdown. Exception: a task
  warrants its own inline integration test when the user explicitly asks for it,
  or when the agent judges — based on the granularity of the project's existing
  integration tests — that this task's behavior is naturally covered at that
  level (e.g. the project already has a per-endpoint integration test and this
  task adds an endpoint). When the agent makes that call, state the reasoning
  and let the user override.
- **Integration / performance / property tests** are heavier — consult the
  project's test setup. In most cases suggest running them manually rather than
  triggering automatically. At the end of implementation, remind the user which
  heavier suites should be run.
- **Integration test coverage**: before implementation begins, check whether existing
  integration tests can be easily extended to cover the new feature. If the feature
  requires significant new test infrastructure (new fixtures, containers, test harnesses),
  flag this early — include it in the task breakdown and consult with the user on scope.

### Self-review suite

The self-review suite is **opt-in** — it never runs automatically. It runs on a
diff scope: either a single task's diff (when the user accepts the offer in
step 6) or the union of all `pending` tasks' diffs (a batch run, see below).

Default suite (run in parallel — single message, multiple Agent calls):

1. **Spec-conformance review** — reads 1-requirements.md + 2-design.md + the
   diff. Verifies every acceptance criterion in scope is satisfied, the
   implementation matches the design (component boundaries, interfaces, data
   flow), and no constraint or non-goal is violated. Findings default to
   **High** — building the wrong thing is the worst class of bug.
2. **Correctness review** — off-by-one, null/empty handling, error propagation,
   race conditions, resource leaks, incorrect assumptions about library behavior.
3. **Security review** — injection, authz, secrets, input validation, common
   OWASP issues.

Opt-in extras — add when the diff warrants it (state which you're adding and why):
- **Clean code review** — naming, structure, abstractions, dead code. Add for
  large or structurally complex tasks.
- **Test-quality review** — tautological tests (mock returns X, assert X),
  missing edge cases (empty, null, boundary, error paths, concurrency), tests
  that assert "no exception" instead of behavior, over-mocking. Add when the
  task added or changed meaningful test logic.
- **Performance review** — only if 1-requirements.md states perf criteria
  (latency, throughput, memory). Otherwise skip; speculative perf review is noise.
- **Docs/README sync** — quick check whether business-logic changes require a
  README or external doc update. One pass, not a full agent.

Use the user's project-specific agents where defined (e.g. `clean-code-reviewer`,
`security-reviewer`). For reviews without a dedicated agent, dispatch the generic
`Agent` tool with an inline prompt scoped to that review's concern. Synthesize
findings into one report grouped by severity (Critical / High / Medium / Low).

**Handling findings:**
- **Critical / High**: fix automatically without asking. Apply fixes, re-run the
  completion gate (build + tests), and note each fix in the report ("auto-fixed").
  If a fix requires a design decision or is ambiguous, stop and ask rather than
  guessing — but this should be rare for well-scoped findings.
- **Medium / Low**: present to the user for a decision; do not fix silently.

**Findings file**: if the total finding count (across all severities, before fixes)
is 5 or more, save the full report to `specs/<NNN>-<name>/review-findings.md`.
Keep it even after fixes — it's a record of what was found and resolved.

After handling findings, update the `Review:` stamp of every task in scope
(`passed` — noting any auto-fixed or accepted items).

### Batch review (`review <name>`)

When tasks shipped with `Review: pending`, run the self-review suite once over
the union of their diffs instead of per task. This is the "review everything
afterwards" path: implement fast with reviews deferred, then do one review pass.
After it runs, update every covered task's stamp to `passed` (noting accepted
findings). The `review <name>` subcommand drives this; the user can also just
say "review the pending tasks".

### Finishing up

When the last task is checked off:

- **All-in-one / Auto-commit mode**: set spec status to `reviewing`, then
  automatically run the batch self-review suite over all tasks (no prompt needed —
  the user chose a hands-off mode). Auto-fix Critical/High findings per the suite
  rules. Present the findings report (with fixes noted), then ask for approval.
- **Pause mode**: if any tasks are still `Review: pending`, remind the user and
  recommend running `review <name>` before approving.

**Always request explicit approval before marking anything `implemented`.**
Present a brief summary: tasks completed, review findings (if the suite ran), any
accepted findings. Then ask: "Ready to mark this feature as implemented?"

The user will often request changes at this point. Treat them like any mid-flight
steer (Tweak / Amendment / Pivot) and apply the same rules: classify the change,
update the relevant spec section(s) in the same diff as the code, do not let spec
and code diverge. Don't mark `implemented` until those changes are done and the
user re-confirms.

Once the user approves:
- If every task is `Review: passed` (or the user accepted outstanding findings),
  set status to `implemented` in 1-requirements.md, 2-design.md, and 3-tasks.md.
  The specs are now reference documentation.
- If any task is still `Review: pending`, the feature can still be marked
  `implemented`, but say so explicitly and recommend a batch `review <name>`
  before the work is considered done. Never silently treat unreviewed code as
  reviewed.
- Do not set `implemented` while a review run surfaced unresolved High or
  Critical findings.

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
| `roadmap <project>` (alias `breakdown`) | Project decomposition: high-level description → ordered list of vertically-sliced features in `specs/roadmap.md`. Greenfield or large brownfield change. Each feature then runs `spec <name>`. Stops at roadmap approval. |
| `spec <name>` | Full 3-phase spec flow — produces 1-requirements.md → 2-design.md → 3-tasks.md in one pass, then **stops at a single spec-set approval gate.** Spec set is the deliverable. |
| `design <name>` | Skip requirements, produce 2-design.md → 3-tasks.md, then stop at spec-set approval. |
| `tasks <name>` | Skip to task breakdown (design exists or is trivial). Stops at spec-set approval. |
| `bugfix <name>` | Abbreviated, **test-first** flow using `./references/bugfix-template.md` — root cause → reproduction → failing regression test → fix approach → tasks. If the user can't describe repro steps, propose exploratory tests from code-reading hypotheses. No requirements phase. |
| `implement <name>` | Begin implementation against an approved spec set. Runs Phase 3.5 (analyze) then implements one task per turn. Use this when the spec is reviewed and ready to build. |
| `review <name>` | Run the self-review suite over all tasks marked `Review: pending` in `specs/<NNN>-<name>/3-tasks.md`, then stamp them `passed`. Use after implementing with reviews deferred. |
| `resume <name>` | Read existing specs from `specs/<NNN>-<name>/`, determine current state, and continue (see below). Also accepts just `resume <NNN>` — search for the directory matching that number. |

**Numbering new specs:** If `specs/roadmap.md` exists and lists this feature, use the
`NNN` it already assigned. Otherwise, before creating `specs/<NNN>-<name>/`, run
`ls specs/` (or the project's equivalent path) to find the highest existing `<NNN>`,
then increment by one. If `specs/` doesn't exist yet, create it and start at `001`.

**Resuming (`resume <name>`):** Search `specs/` for a directory matching the name or
number. Read all spec files inside. Determine the current state by checking:
1. Which spec files exist and their `> Status:` line.
2. If 3-tasks.md exists and is approved, how many tasks are checked off.
3. How many checked-off tasks are still `Review: pending` (shipped but unreviewed).
4. If any spec is in `draft` status, it was being worked on or needs re-approval.

Present a brief summary of where things stand ("1-requirements.md approved, 2-design.md approved,
3 of 8 tasks complete, 2 pending review — next up is task 4: ...") and confirm with
the user before continuing. If tasks are pending review, mention `review <name>`.

`breakdown` is an alias for `roadmap` — route both to Phase 0.

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
