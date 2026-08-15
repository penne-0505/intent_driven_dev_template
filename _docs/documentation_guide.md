---
title: Documentation Guide
status: active
draft_status: n/a
created_at: 2025-12-07
updated_at: 2026-08-15
references:
  - "_docs/standards/documentation_guidelines.md"
  - "_docs/standards/documentation_operations.md"
  - "_docs/standards/quality_assurance.md"
  - "_docs/standards/security_for_agents.md"
related_issues: []
related_prs: []
---

# Documentation Guide

**必読:** ループと QA の判断基準は `_docs/standards/quality_assurance.md`、運用ルールは
`_docs/standards/documentation_operations.md` を参照して遵守してください。

## このガイドの位置づけ

- このプロジェクトでドキュメントを作成・更新する際の要点をまとめたクイックリファレンスです。
- 詳細な執筆指針は `_docs/standards/documentation_guidelines.md`、運用プロセスは
  `_docs/standards/documentation_operations.md`、ループ・QA・コメント規則は
  `_docs/standards/quality_assurance.md` を確認してください。

## 中核の考え方

このテンプレートは intent-driven development の土台です。ドキュメントは意図 (why / why not) を
運ぶ媒体であり、読者はコンテクストを持たない coding agent です。

- すべての変更が最小ループを回ります: `TODO (AC) → 実装 → Intent Delta → QA round`。
- 省略できるのは深さであって、存在ではありません。自明な変更は理由付き `None:` で閉じます。
- コードから意図への経路はポインタコメント (`// intent: DEC-xxx — 因果一行`) のみです。
  散文コメントは書けません。書きたい散文は DEC に書くべき why か、捨ててよい how です。

## Canonical Paths

```text
_docs/plan/<Area>/<slug>/plan.md            # Size >= M のみ
_docs/intent/<Area>/<slug>/decision.md      # 設計判断 (DEC)。恒久
_docs/intent/<Area>/conventions/decision.md # 横断的な原則 (candidate 含む)
_docs/qa/<Area>/<slug>/qa.md                # QA 計画 + 検証記録。恒久
_docs/qa/<Area>/maintenance.md              # 微小変更の round 集約
_docs/guide/<Area>/<slug>/usage.md          # 必要時のみ
_docs/reference/<Area>/<slug>/reference.md  # 必要時のみ
_docs/archives/plan/<Area>/<slug>/...       # 完了タスクの plan
```

`<Area>` は `TODO.md` の `Area` と一致させ、`<slug>` は機能・変更単位の kebab-case 名にします。

## よくある更新パターン

### 0. 微小な変更 (typo、依存更新など)

1. 実装する。
2. `_docs/qa/<Area>/maintenance.md` に round を 1 つ追記する (実行コマンド、Intent Delta:
   `None: <理由>` または `applied: DEC-xxx`、verdict)。数行でよい。

### 1. 通常の変更

1. TODO の AC / Steps を確認する。
2. 実装する。設計判断が生まれたら DEC を intent に記録し、該当箇所へポインタコメントを置く。
3. `_docs/qa/<Area>/<slug>/qa.md` に round を記録する (Intent Delta 必須)。
4. DEC 新設 / `Size >= M` / `Risk High` なら `R2: PENDING` を書き、R2 タスクを TODO に積む。

### 2. `Size >= M` の変更

- 実装前に `_docs/plan/<Area>/<slug>/plan.md` を作成する。
- `Risk >= Medium` なら qa.md を実装前に `qa_status: planned` で書き始める。
- TODO の `Plan`, `Intent`, `QA` を root-relative canonical path で更新する。

### 3. R2 タスクを拾ったとき

TODO の R2 タスクに従い、diff とリポジトリ内の docs だけを読んで固定設問 4 つに答え、結果と
gap を該当 qa.md の round に追記する。gap があれば DEC 修繕タスクを TODO に積む。

### 4. 完了処理

- verdict を確認する (`PASS`、または残リスク明示の `PARTIAL` のみ完了可)。
- TODO からタスクを削除する。完了履歴の正典は QA round であり、Done セクションは作らない。
- plan があれば `_docs/archives/plan/` へ移送し、参照を更新する。

## Front-matter クイックリファレンス

共通 (standards 配下を除く): `title` / `status` / `created_at` / `updated_at` / `references` /
`related_issues` / `related_prs`。

QA 文書は追加で `qa_status` / `risk`。schema marker は新規作成時 `intent_schema: 3` /
`qa_schema: 4`。旧 schema 文書は「見える未完了」として warning 報告の対象になります。

## 検証コマンド

```bash
./scripts/check-docs.sh
```

ローカル検証の正典は `check-docs.sh` です。Deno があれば十分で、CI も同一 script を通します。

## トラブルシューティング

| 状況 | 対応 |
| --- | --- |
| Plan が必要か分からない | `Size >= M` なら必須。それ未満は TODO の AC / Steps で足りる。 |
| Intent Delta に何を書くか分からない | 新しい判断をしたなら DEC 新設。既存の判断の適用なら `applied:`。どちらでもなければ理由付き `None:`。 |
| verdict が `FAIL` / `BLOCKED` | TODO を削除せず、修正または follow-up TODO を追加する。 |
| QA docs を archive したい | しない。`status: obsolete` / `superseded` にする。 |
| コメントに説明を書きたい | 散文は禁止。why なら DEC に書いてポインタを置く。how なら書かない。 |
| 作業メモを残したい | 運用文書には残さない。必要なら `_meta/` (非運用) か、その場で intent / QA へ。 |
