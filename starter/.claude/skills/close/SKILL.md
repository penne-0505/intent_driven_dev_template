---
name: close
description: Use after implementation work — records the QA round (Intent Delta, R2, principles, verdict), reviews the change, and decides task completion.
---

# Close

Run after implementation, before the final response. The norms live in
`_docs/standards/quality_assurance.md` — this skill is the procedure, not the rulebook.

## Procedure

1. **Verify.** Run the planned checks and `./scripts/check-docs.sh`. Compare the diff
   against the request, TODO Goal, and Acceptance Criteria.
2. **R1 review** (quality_assurance.md § R1): switch to a reviewer stance — or hand to a
   different model when available — and check: DEC conformance, pointer placement,
   whether a `None:` Intent Delta is actually justified.
3. **Record the Round** in the task's `qa.md`, or in `_docs/qa/<Area>/maintenance.md` for
   small work (format: quality_assurance.md § QA 文書). Every change gets a Round.
4. **R2** (quality_assurance.md § R2): if triggered (new DEC / `Size >= M` / `Risk High`),
   write `R2: PENDING` and add an R2 task to TODO.md — or run it synchronously if this
   harness can spawn an isolated fresh-context call (pass only the commit range and QA doc
   path; grade the blind answers before completion).
5. **Reflect** (quality_assurance.md § transferable principle): record a candidate or a
   reasoned `None:`. Candidates go to conventions with the `(candidate)` mark and must be
   presented in the final summary (principle / origin / impact / risks / recommendation).
6. **Decide completion.** `PASS` (or accepted `PARTIAL`) → remove the TODO task, add
   follow-ups and the R2 task. `FAIL` / `BLOCKED` → the task stays.
7. **Archive the Plan** if one exists: `git mv` to `_docs/archives/plan/<Area>/<slug>/`
   and update references. Never archive intent / QA / guide / reference — mark obsolete
   ones `status: superseded` / `obsolete` instead.
8. **Summarize**: validations actually run, verdict, candidates presented.

## Turn-End Conduct

A turn may end with documentation debt; a task may not close with it. If docs are behind at
turn end, state the gap in one line and handle it at the head of the next mainline
instruction — do not start unprompted work, and do not re-ask for permission the loop
already grants.
