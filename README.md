# SDD Skill

> Work in progress.

A personal skill that tailors Spec-Driven Development to my workflow and preferences.
Not a generic SDD framework — opinions are baked in.

## What it does

Guides an agent through a structured spec-before-code workflow: requirements → design → tasks → implementation.
Specs live as markdown files in the repo, versioned alongside code, and serve as lightweight documentation after the feature ships.

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill instructions — loaded by the agent when SDD is triggered |
| `AGENT-SNIPPET.md` | Paste into a project's agent instructions file (CLAUDE.md, AGENTS.md, etc.) to enable SDD in that project |
| `references/requirements-template.md` | Template for the requirements phase |
| `references/design-template.md` | Template for the design phase |
| `references/tasks-template.md` | Template for the task breakdown phase |
| `references/bugfix-template.md` | Template for the abbreviated bug fix flow |
| `references/research-template.md` | Template for the optional research doc |

## Usage

In Claude Code, trigger naturally ("let's spec this out") or explicitly with `/sdd <subcommand>`:

```
/sdd spec <feature-name>    full flow: requirements → design → tasks → implement
/sdd design <feature-name>  skip requirements, start from design
/sdd tasks <feature-name>   skip to task breakdown
/sdd bugfix <name>          abbreviated bug fix flow
/sdd resume <name>          continue from existing specs
```

For other agents, paste `AGENT-SNIPPET.md` into the project's instructions file.
