# Migration Template

```markdown
# <Feature Name> — Migration

## Summary
One or two sentences: what changes for existing data/clients/deployments, and
whether the rollout is reversible.

## Manual steps
Ordered actions an operator must perform by hand. Number them — order matters.
1. <step> (e.g. run backfill script `scripts/backfill_x.sql`)
2. <step> (e.g. flip feature flag `enable_x` after deploy)
3. <step>
(Omit if there are no manual steps.)

## Backward compatibility
How existing data, clients, and callers keep working during and after rollout.
- **Data**: schema/format changes and how old rows are read (defaults, dual-read,
  backfill).
- **Clients/API**: how existing callers keep working — new fields optional,
  deprecation window, versioning.
- **Deploy sequencing**: order services must ship in; dual-write/dual-read windows.
(Omit any bullet that doesn't apply.)

## Rollback
How to undo if the rollout goes wrong, and any point past which rollback is no
longer safe (e.g. after a destructive backfill). State "reversible — revert deploy"
if it's that simple.

## Verification
How to confirm the migration succeeded — row counts match, old and new clients
both work, no error spike.
```

## Guidance

- **Optional doc — create it only when the change needs one.** A feature that ships
  cleanly (no manual steps, no data reshaping, backward-compatible by construction)
  does not need a migration.md. Create it when there are hand-run steps, a data
  backfill/reshape, a deprecation, or a deploy-ordering constraint.
- Distinct from design.md's "Schema changes": design says *what the new shape is*;
  migration says *how you get existing systems there without breaking them*.
- **Manual steps must be ordered and copy-pasteable** — an operator follows them
  under pressure. Reference actual script paths, flag names, and commands, not
  vague descriptions.
- Always state the rollback story, even if it's "just revert the deploy." The
  worst time to figure out rollback is mid-incident.
- Keep it short. If it's growing past ~40 lines, the migration is complex enough
  to warrant its own tasks in 3-tasks.md rather than more prose here.
