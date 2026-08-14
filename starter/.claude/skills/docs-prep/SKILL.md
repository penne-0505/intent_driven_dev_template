---
name: docs-prep
description: Use after implementation-prep when the change is Size >= M or introduces design decisions that need a Plan.
---

# Documentation Preparation

This skill prepares the Plan (and intent scaffolding) for large work. For `Size < M`,
skip it — the TODO's AC / Steps are the plan, and decisions get recorded as DECs
when they emerge during implementation.

## When to Use

- `Size >= M` (Plan is mandatory)
- Breaking changes or migrations that need Scope / Non-Goals / Rollout thinking

## Workflow

### 1. Create or Update the Plan

Location: `_docs/plan/<Area>/<slug>/plan.md` — Overview / Scope / Non-Goals / Requirements /
Tasks / QA Plan / Deployment. The Plan is coordination material; its why-content will be
distilled into DECs. Use root-relative references.

### 2. Intent scaffolding

If the design already contains committed decisions, record them as DECs now
(`_docs/intent/<Area>/<slug>/decision.md`, `intent_schema: 3`, repository-unique IDs —
allocate max existing ID + 1). Decisions that emerge later are recorded when they emerge;
the Intent Delta in the QA round keeps them from being silently dropped.

Intent documents record why / why not / change freedom. Do not restate current values or
reproduce mechanism detail the code itself shows.

### 3. QA

Run `qa-prep` to create `_docs/qa/<Area>/<slug>/qa.md` with `qa_status: planned`.

### 4. TODO

Update the task's `Plan` / `Intent` / `QA` fields with canonical paths.

## Lifecycle

After the task completes, the Plan is moved to `_docs/archives/plan/<Area>/<slug>/` with
`git mv` and references are updated. Intent and QA documents are permanent and never move.
