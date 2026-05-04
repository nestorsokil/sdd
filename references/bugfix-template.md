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

## Reproduction
- Trigger: exact steps, inputs, or conditions that surface the bug.
  If unknown, mark as "unconfirmed" and describe the **symptom** instead — the
  reproduction will be discovered via an exploratory test (see below).
- Observed: what currently happens (error, wrong value, crash, log line)
- Expected: what should happen instead

## Regression test
- Type: unit / integration / property / e2e
- Location: `path/to/test/file`
- What it asserts: the precise behavior the bug violates
- Why this level: why unit vs. integration (e.g. "needs real DB to hit the
  ordering issue", "pure function — unit suffices")
- **If repro is unknown**: this starts as an *exploratory* test — a hypothesis
  about what triggers the bug. Run it; if it doesn't fail, refine the
  hypothesis and try again. Promote to a regression test once it reliably
  reproduces the observed symptom.

## Fix approach
How the fix works. Pseudo-code or plain description — not implementation details.
If multiple approaches were considered, note why this one was chosen.

## Tasks
- [ ] [small] **1. Add failing regression test** — <assertion> (`path/to/test/file`)
- [ ] [small] **2. Run test, confirm it fails for the right reason** — output should match the observed bug, not an unrelated error
- [ ] [small] **3. <Fix title>** — <what to do> (`path/to/file`)
- [ ] [small] **4. Run test, confirm it passes** — and full relevant suite stays green

## Verification
How to confirm the bug is gone in addition to the regression test (manual repro,
log inspection, metric to watch post-deploy).
```

## Guidance

- No requirements phase — the bug report *is* the requirement.
- **Test-first is mandatory.** Before drafting tasks, ask the user:
  1. **Repro specifics** — exact trigger (input, sequence, environment) if not
     already clear from the bug report. Don't guess; a test built on a guessed
     repro proves nothing.

     **If the user only has a plain-language description and doesn't know how
     to reproduce it** (common — they saw a symptom, a log, a customer report):
     do NOT ask them to figure it out. Instead:
       - Read the relevant code paths and form 1-3 hypotheses about what could
         produce the symptom.
       - Propose an **exploratory test** for the most likely hypothesis:
         "I think this happens when X. I'll write a test that calls Y with X
         and asserts the symptom appears. If it fails, we have repro. If not,
         I'll refine and try the next hypothesis."
       - Get user confirmation on the hypothesis before writing the test.
       - Iterate: if the first exploratory test doesn't reproduce, narrow or
         pivot the hypothesis (don't just keep guessing — re-read code, ask
         the user for any extra signal: timestamps, env, user role, data shape).
       - Once a test reliably reproduces the symptom, it becomes the regression
         test and the repro section gets filled in.
  2. **Test type** — unit, integration, property, e2e. Recommend one based on
     where the bug lives (pure logic → unit; cross-component or stateful → integration;
     input-space bug → property), then confirm. If the project lacks the harness
     for the recommended type, surface that and offer alternatives.
  3. **Scope of the assertion** — the *exact* behavior to assert. "Login works"
     is not an assertion. "POST /login with expired token returns 401, not 500" is.
  4. **Existing coverage** — check whether a nearby test can be extended rather
     than creating a new file. Mention what you found.
- Root cause must be specific before writing tasks. "Something is wrong with auth"
  is not a root cause. "Token expiry is checked after the DB call, so expired tokens
  reach the DB" is.
- The failing test must fail *for the bug's reason*, not an unrelated error
  (typo, missing import, wrong fixture). Verify the failure mode matches the
  observed symptom before writing the fix.
- If the fix touches more than 3 files or requires schema changes, consider whether
  this is actually a feature, not a fix — and use the full SDD flow instead.
- Skip the regression test only if the user explicitly opts out (e.g. throwaway
  script, code being deleted next week). Record the opt-out in the spec.
