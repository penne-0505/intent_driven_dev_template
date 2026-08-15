---
name: implementation-prep
description: Use before any implementation request that touches multiple files or requires TODO/document alignment.
---

# Implementation Preparation

This skill aligns the request with the always-on loop before implementation begins:
`TODO (AC) → implement → Intent Delta → QA round`. Presence is unconditional; only depth varies.

## Preparation Flow

1. **Clarify the request.** Restate the goal, assumptions, and open questions.
2. **Document reconnaissance.** Read AGENTS.md, the relevant standards, intents, and QA docs
   before editing code. Grep `_docs/intent/` for DEC entries that already govern the area —
   re-inventing an existing decision under a new ID is the main failure mode to avoid.
3. **TODO.md audit.** Confirm the task has `Size`, `Risk`, `Acceptance Criteria` (AC-001 style),
   and a `QA` path (a dedicated `qa.md`, or `_docs/qa/<Area>/maintenance.md` for small work).
   `QA: None` is invalid.
4. **Depth gate.**
   - `Size >= M`: create the Plan first (run `docs-prep`), and use a dedicated qa.md.
   - `Risk >= Medium`: run `qa-prep` so qa.md exists with `qa_status: planned` before coding.
   - Touching `scripts/`, `.github/`, `_docs/standards/`, agent configs, or AGENTS.md?
     The path-based floor makes this `Risk High` regardless of the declared value.
5. **Plan the pointers.** Where the implementation will embody a decision (especially a why-not
   or intentional omission), plan a `// intent: DEC-xxx — <causal why>` anchor. Remember:
   prose comments are banned; anything you want to explain belongs in a DEC or nowhere.

## Deliverables Before Implementation

- Confirmed TODO entry with AC and QA path.
- Plan / qa.md when depth requires them.
- List of existing DECs that govern the change (`applied:` candidates for the Intent Delta).
- Open questions or blockers.
