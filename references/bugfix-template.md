# Bugfix Template

```markdown
# <Bug Name> — Bugfix
> Status: draft

## Root cause
One paragraph: what is broken and why. Be specific — vague root causes produce
vague fixes.

## Impact
- What breaks / who is affected
- Severity and scope (isolated vs. systemic)

## Fix approach
How the fix works. Pseudo-code or plain description — not implementation details.
If multiple approaches were considered, note why this one was chosen.

## Tasks
- [ ] [small] **1. <Title>** — <what to do> (`path/to/file`)
- [ ] [small] **2. <Title>** — <what to do> (`path/to/test`)

## Verification
How to confirm the bug is gone. Ideally a test that would have caught it.
Should the test be added to prevent regression?
```

## Guidance

- No requirements phase — the bug report *is* the requirement.
- Root cause must be specific before writing tasks. "Something is wrong with auth"
  is not a root cause. "Token expiry is checked after the DB call, so expired tokens
  reach the DB" is.
- If the fix touches more than 3 files or requires schema changes, consider whether
  this is actually a feature, not a fix — and use the full SDD flow instead.
- Verification should answer: "how would we have caught this earlier?" If a test
  would help, add it as the last task.
