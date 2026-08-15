---
name: post-implementation
description: Use after code changes are complete and before final response or PR summary.
---

# Post-Implementation

This skill closes implementation work. The canonical record of completion is the QA round;
the final summary is volatile and only points at it.

## Closure Flow

1. **Verify completion.** Compare the diff against the request, TODO Goal, and Acceptance Criteria.
2. **Record the loop.** Run `qa-review`: append the Round (Intent Delta / R2 / Transferable
   Principles / Verdict) to qa.md or maintenance.md. Every change gets a Round — a typo fix's
   Round is three lines, but it exists.
3. **Check the pointers.** Non-obvious code that embodies a decision carries
   `// intent: DEC-xxx — <causal why>`. No prose comments anywhere
   (`./scripts/check-docs.sh` enforces this).
4. **Decide completion.** Remove the TODO task only with a `PASS` (or accepted `PARTIAL`)
   verdict. Add follow-up tasks for residual work, and the R2 task when triggered.
5. **Archive the Plan.** For `Size >= M`, `git mv` the plan into `_docs/archives/plan/` and
   update references.
6. **Update guide / reference** only if user-facing behavior or durable mechanism knowledge
   changed. Not creating them is not a violation.
7. **Run validation.** `./scripts/check-docs.sh` — state the actual result.

## Session-End Reflection (Transferable Principles)

Before the final summary, ask: **did any decision this session make you stop and think
"why is it like this"?** If a principle emerges that outlives the session, record it as a
candidate; otherwise write `None: <why nothing emerged>` in the Round. Never leave it blank
and never write a bare `None`.

Candidates may be appended to `_docs/intent/<Area>/conventions/decision.md` with the
`(candidate)` mark — unratified, not a rule, never to be anchored from code. Present every
candidate in the final summary using the five-part format (principle / origin / impact /
risks / recommendation). Ratification is the user's explicit act.

## Turn-End Conduct

A turn may end with documentation debt; a task may not close with it. If docs are behind when
the turn ends, say so in one line and wait — do the catch-up at the head of the next mainline
instruction, without asking for permission you already have.

## Deliverables

- QA round recorded with verdict and Intent Delta.
- TODO.md updated (completed tasks removed, follow-ups and R2 tasks added).
- Candidates presented, or a reasoned `None:` in the Round.
- Final summary listing validations actually run.
