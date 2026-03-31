# SDD — agent instructions snippet
#
# Paste this into your project's agent instructions file (e.g. CLAUDE.md, AGENTS.md,
# GEMINI.md, or equivalent).
# Replace <path-to-sdd-skill> with the actual path where you installed the skill
# (e.g. ~/.claude/skills/sdd for Claude Code).

## Spec-Driven Development

This project uses spec-driven development. Read `<path-to-sdd-skill>/SKILL.md`
before acting on any of the commands below.

### Trigger patterns

Invoke explicitly with `/sdd <subcommand>`, or just describe what you want in natural language
and the agent will pick the right flow.

| Subcommand | What it does |
|------------|-------------|
| `spec <name>` | Full flow: requirements → design → tasks → implement |
| `design <name>` | Skip requirements, start from design |
| `tasks <name>` | Skip to task breakdown |
| `bugfix <name>` | Abbreviated bug fix flow |
| `resume <name>` | Pick up existing specs from `specs/<name>/` |

