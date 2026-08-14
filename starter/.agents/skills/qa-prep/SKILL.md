---
name: qa-prep
description: Use before or during implementation to start the unified qa.md from TODO, Plan, and Intent.
---

# QA Preparation

This skill starts the unified QA document (`qa_schema: 4`) before implementation goes too far.
Planning and verification live in one file; starting early is the "depth" that risk buys.

## Trigger Conditions

- `Risk >= Medium` (mandatory: qa.md must exist with `qa_status: planned` before coding)
- `Size >= M` (the dedicated qa.md accompanies the Plan)
- Security / auth / privacy / payment / data safety / migration changes
- Changes under `scripts/`, `.github/`, `_docs/standards/`, agent configs, or AGENTS.md
  (path-based `Risk High` floor applies)

## Required Procedure

1. Read the TODO task, the Plan (if any), and the governing DECs.
2. Create `_docs/qa/<Area>/<slug>/qa.md` from `_docs/standards/templates/qa.md` with
   `qa_status: planned`.
3. Write Acceptance Criteria (synced with TODO) and the Checks table: every core AC and
   applicable INV gets a check type (automated test / validator / manual / diff review).
4. For `Risk High / Critical`, include rollback / recovery / data safety / security checks.
5. For Bug tasks plan a regression test or a no-test rationale; for Refactor tasks plan
   behavior-preservation checks; for workflow changes plan agent misbehavior checks.
6. Update the TODO task's `QA:` field.

## Rules

- Checks are written before implementation and are not rewritten to match outcomes afterwards —
  results go into appended Rounds.
- Do not turn current values, algorithms, or file layouts into check contracts unless a DEC
  says the exact value is the contract.
- Do not push everything into manual QA when automated checks are feasible.
