---
name: test-maintenance
description: Use while adding or updating tests so they remain mapped to intent, AC, and invariants.
---

# Test Maintenance

This skill keeps executable tests connected to the design record.

## Required Procedure

1. Read the task's qa.md Checks table.
2. Inspect the existing test structure.
3. Check whether each core AC and applicable INV has a corresponding test or check path.
4. Add or update missing tests in the codebase's standard test locations
   (`_docs/qa/` never holds test code).
5. For Bug fixes, add a regression test or record a no-test rationale in the Round.
6. For Refactors, prioritize behavior-preservation checks.

## Policy

- Tests map to acceptance criteria and invariants, not implementation details. Do not assert
  an exact value, algorithm, or representation unless a DEC identifies it as the contract.
- Use snapshots only when they protect intentional output and the governing DEC is clear.
- Include IDs in test names or `// Covers AC-001 / INV-002` comments when practical —
  these are the only comment forms allowed in test code besides intent pointers and pragmas.

## Test vs Pointer Split

- If a strict invariant can fail a test, assign it to a test named with its INV ID.
- If a decision cannot be asserted by a test — a why-not, an intentional omission, a
  structural choice — anchor it with `// intent: DEC-xxx — <causal why>` instead.
- Do not enforce the same condition through both a test and a pointer.
