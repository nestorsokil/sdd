# SDD — agent instructions snippet
#
# Paste this into your project's agent instructions file (e.g. CLAUDE.md, AGENTS.md,
# GEMINI.md, or equivalent).
# Replace <path-to-sdd-skill> with the actual path where you installed the skill
# (e.g. ~/.claude/skills/sdd for Claude Code).

## Spec-Driven Development

This project uses spec-driven development. Read `~/.claude/skills/sdd/SKILL.md`
before acting on any of the commands below.

### Trigger patterns

Invoke explicitly with `/sdd <subcommand>`, or just describe what you want in natural language
and the agent will pick the right flow.

| Subcommand | What it does |
|------------|-------------|
| `spec <name>` | Spec flow only: requirements → design → tasks. Stops at tasks.md approval. |
| `design <name>` | Skip requirements, start from design. Stops at tasks.md approval. |
| `tasks <name>` | Skip to task breakdown. Stops at tasks.md approval. |
| `bugfix <name>` | Abbreviated, test-first bug fix flow. |
| `implement <name>` | Build against an approved spec set, one task per turn. |
| `review <name>` | Run the self-review suite over tasks with deferred (`pending`) reviews. |
| `resume <name>` | Pick up existing specs from `specs/<name>/`. |
