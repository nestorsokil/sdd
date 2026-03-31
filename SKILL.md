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
across sessions and are version-controlled alongside code.

## Why this exists

Jumping straight to code produces working-but-wrong solutions — code that compiles
but misses edge cases, violates constraints, or solves the wrong problem. SDD forces
a think-then-build cadence: each phase produces a short markdown artifact the human
reviews before the next phase begins.

## The three phases

```
requirements.md  →  design.md  →  tasks.md  →  implementation
     ↑ approve        ↑ approve      ↑ approve      ↑ one task at a time
```

Every phase ends with an explicit approval gate. Do NOT advance to the next phase
until the user says "approved", "lgtm", "go", "next", "y", or similar affirmative.

If the user asks to skip a phase (e.g., "skip requirements, I know what I want"), that's
fine — acknowledge which phase you're skipping and proceed to the next one.

## File layout

```
specs/
  <feature-name>/
    requirements.md
    design.md
    tasks.md
    research.md          (optional — only when design alternatives were discussed)
    diagrams/            (optional — drawio files + exported images)
      *.drawio
      *.png / *.svg
```

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
  - `draft` — being written or revised
  - `approved` — user has signed off; do not edit without re-opening
  - `implemented` — all tasks complete; spec is now reference documentation

---

## Phase 1: Requirements (`requirements.md`)

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

## Phase 2: Design (`design.md`)

Goal: capture *how* the feature will be built — components, interfaces, data flow.

Read the template: `./references/design-template.md`

Key principles:
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
  bloating this doc — but keep a brief summary here so design.md stays self-contained.
- If the feature touches persistence, include schema. If it touches APIs, include
  request/response shapes.

After writing, present the doc and wait for approval. On approval, set status to `approved`.

## research.md (optional)

Create this file when competing design approaches were actively discussed during spec
creation. It captures the full exploration: options, trade-offs, and why the chosen
direction won.

Do NOT create it for routine decisions. Only when real back-and-forth happened and the
fuller context is worth preserving as documentation.

## Phase 3: Tasks (`tasks.md`)

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

After writing, present the doc and wait for approval. On approval, set status to `approved`.

## Implementation phase

Once tasks.md is approved, implement one task at a time:

1. State which task you're starting (by number and title).
2. Implement it.
3. Check it off in tasks.md: `- [x] Task description`.
4. Briefly state what changed and what's next.
5. Continue to the next task unless the user intervenes.

When the last task is checked off, set status to `implemented` in requirements.md,
design.md, and tasks.md. The specs are now reference documentation.

If during implementation you discover the design is wrong or incomplete, STOP.
Explain what you found and suggest an amendment to design.md or tasks.md before
continuing. Catching design issues during implementation is exactly why the specs exist.

## Handling mid-flight changes

**Requirements change after approval:**
Set the affected doc's status back to `draft`, make the change, present the delta,
and get re-approval. Propagate to design.md and tasks.md if affected — re-approve
each touched doc. Don't silently update a spec; the approval gate keeps the human
in the loop.

**Design turns out wrong during implementation:**
Stop. Amend design.md (and tasks.md if the task list changes), set status back to
`draft`, get re-approval, then continue.

---

## Entry points

When invoked as `/sdd $ARGUMENTS` or via natural language, route based on the first word:

| Subcommand | Behavior |
|------------|----------|
| `spec <name>` | Full 3-phase flow starting from requirements |
| `design <name>` | Skip requirements, start from design |
| `tasks <name>` | Skip to task breakdown (design exists or is trivial) |
| `bugfix <name>` | Abbreviated flow using `./references/bugfix-template.md` — root cause → fix approach → tasks, no requirements phase |
| `resume <name>` | Read existing specs from `specs/<name>/` and continue where left off |

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
