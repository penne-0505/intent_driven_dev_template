---
name: docs-cleanup
description: Use after post-implementation when a completed Size >= M task leaves a Plan to archive or stale doc states to reconcile.
---

# Documentation Cleanup

This skill finalizes documentation state after substantial work. With the always-on loop,
most closure happens in `qa-review` / `post-implementation`; what remains here is the Plan
archive and status reconciliation.

## Archive Checklist

Only Plans of completed tasks are archived. Do not archive anything else.

1. The task is complete (Round with `PASS` / accepted `PARTIAL` exists).
2. `git mv _docs/plan/<Area>/<slug>/ _docs/archives/plan/<Area>/<slug>/`
3. Update `references` in docs that pointed at the live plan path.
4. Confirm the diff shows the move and no live copy remains.

Do not use `rm` / `git rm`. Archive movement is history-preserving relocation, not deletion.

## Do not archive

- `_docs/intent/**` — permanent decision log.
- `_docs/qa/**` — permanent quality records.
- `_docs/guide/**` / `_docs/reference/**` — live documents.

When any of these becomes obsolete, set `status: obsolete` or `status: superseded` and,
where a successor exists, point to it via `references`.

## Final Verification

```bash
./scripts/check-docs.sh
```

Record the actual result in the Round.
