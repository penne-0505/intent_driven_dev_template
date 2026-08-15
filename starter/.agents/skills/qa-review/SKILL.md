---
name: qa-review
description: Use after implementation to record the QA round, run R1/R2 review, and decide completion.
---

# QA Review

This skill closes the loop for a change: record the Round, review it, and decide whether
the task can complete.

## Required Procedure

1. Read the TODO task, the diff, the Plan (if any), and the governing DECs.
2. **R1 (validity review).** Switch to a reviewer stance — or hand to a different model if one
   is available (a different training base beats a fresh context of the same model):
   - Does the change serve each affected DEC's `Why` and stay within its `Change freedom`?
   - Are intent pointers placed where a naive reader would "fix" a deliberate shape?
   - Is a `None:` Intent Delta actually justified, or did a decision slip through?
3. Append a **Round** to the task's `qa.md` (or `_docs/qa/<Area>/maintenance.md` for small work):
   - Commands actually run and their results (never list commands that were not run)
   - AC coverage
   - **Intent Delta**: `DEC-xxx 新設` / `applied: DEC-xxx` / `None: <reason>` — never bare None
   - **R2**: see below
   - **Transferable Principles**: candidate or `None: <reason>` — never bare None
   - **Verdict**: PASS / PARTIAL / FAIL / BLOCKED
4. **R2 (reconstruction test).** If the Intent Delta created a DEC, or `Size >= M`, or
   `Risk High`: write `R2: PENDING` in the Round and add an R2 task to TODO.md. The next
   session answers the four fixed questions in `_docs/standards/quality_assurance.md`
   (diff + repo docs only) and appends the result. If this harness can spawn an isolated
   fresh-context run, you may execute R2 synchronously instead — pass only the commit range
   and the QA doc path, never your session context — and grade the blind answers against
   the true why before completion.
5. Set `qa_status` to match the Verdict, and update the TODO task.

## Completion Decision

| Verdict | Decision |
| --- | --- |
| `PASS` | TODO can be removed. |
| `PARTIAL` | Removable only with explicit residual risks and follow-up TODOs. |
| `FAIL` / `BLOCKED` | TODO stays; add corrective tasks or record the blocker. |

## Transferable Principles

- Ask: did any decision this session carry a learning that outlives it? Record 1-3 line
  candidates, or `None: <reason>`. "The fix matches an existing pattern" does not justify
  skipping — why the pattern exists is often the principle.
- A candidate may be appended to `_docs/intent/<Area>/conventions/decision.md` as a
  `### DEC-xxx (candidate):` entry. The candidate mark means "unratified — not a rule".
  Only the user removes the mark. Never anchor code to a candidate DEC.
- **Presentation duty**: every candidate must be presented in the final summary with:
  (1) the principle in 1-2 lines, (2) its origin, (3) impact if adopted, (4) risks or
  counter-cases, (5) your recommendation.

## Turn-End Conduct

A turn may end with documentation debt; a task may not close with it. If docs are behind at
turn end, do not start working on them unprompted — state the gap in one line and handle it
at the start of the next mainline instruction. Task deletion still requires the Round and
Verdict to exist.
