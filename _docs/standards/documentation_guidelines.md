# ドキュメント整備指針

**ドキュメンテーションでわからないことがあれば、まずこのファイルを確認してください。**

## 概要

本指針は、このプロジェクトにおけるドキュメント作成・管理の統一方針を定める。運用プロセスは
`_docs/standards/documentation_operations.md`、QA とループの判断基準は
`_docs/standards/quality_assurance.md` を参照し、本書は日々の執筆の実務指針にフォーカスする。

## ドキュメントの目的

1. **コンテクストを持たない coding agent が、実装の意図 (why / why not) を再構成するため**（主目的）
2. **設計判断が守られているかを検証するため**
3. **人間がレビューや監査で参照するため**

読者の第一想定は、毎回コンテクストが分離された状態で作業を始める coding agent である。
「なぜこの実装はこうなっているのか」という問いが、intent の DEC への参照一回で解消される状態を
理想とする。人間向けの網羅的な解説は目的ではない。

## ドキュメント種別

必須の最小構成はループが要求する 2 種のみで、他は必要になったときに作る。

### `_docs/intent/<Area>/<slug>/decision.md`（ループの中核・恒久）

- **目的**: 将来の変更者が設計判断の why / why not と変更可能範囲を再構成するための一次資料。
- **内容**: Context / リポジトリ一意 ID を持つ DEC（What / Why / Change freedom、必要な
  Why not / Revisit when）/ 任意の Intent-derived Invariants。
- **書き方の正典**: `_docs/standards/quality_assurance.md` の intent decision record と知識の 4 分法。
- **ライフサイクル**: 恒久記録。archive しない。

### `_docs/qa/<Area>/<slug>/qa.md`（ループの中核・恒久）

- **目的**: QA の計画（実装前）と検証記録（実装後）の単一文書。
- **内容**: Acceptance Criteria / Checks / 追記専用の Rounds（Intent Delta、R2、教訓候補、verdict を含む）。
- **微小変更**: slug を切らず `_docs/qa/<Area>/maintenance.md` へ round を追記する。
- **ライフサイクル**: 恒久記録。archive しない。

### `_docs/plan/<Area>/<slug>/plan.md`（`Size >= M` のみ）

- **目的**: 大きな変更の調整・分解（Scope / Non-Goals / Requirements / Tasks / QA Plan / Rollout）。
- **位置づけ**: 意図成分は最終的に intent へ昇華する原典。計画行為自体は TODO の AC / Steps で
  すべての変更に対して行う。
- **ライフサイクル**: タスク完了後に `_docs/archives/plan/` へ移送できる。

### `_docs/guide/` / `_docs/reference/`（必要時のみ）

- **guide**: 利用者向けの使い方・運用手順。ユーザー向け挙動を共有する必要が生じたときに作る。
- **reference**: API 仕様や、コードから再構成できない耐久的な機構解説の辞書的置き場。
- どちらも恒久保守義務はない。作らないことは違反ではない。仕様書を起点に開発を回すことは
  このテンプレートの目的ではない。

## 必須フィールド

`_docs/standards/` 配下を除く運用対象ドキュメントの front-matter は
`_docs/standards/documentation_operations.md` の Front-matter Schema に従う
（共通 7 項目 + QA 文書の `qa_status` / `risk`、schema marker は `intent_schema: 3` /
`qa_schema: 4`）。

## テンプレート

作成用の雛形は `_docs/standards/templates/` にある。

- `_docs/standards/templates/intent.md`
- `_docs/standards/templates/qa.md`
- `_docs/standards/templates/plan.md`
- `_docs/standards/templates/guide.md`
- `_docs/standards/templates/reference.md`

## 更新フロー

1. **すべての変更**: TODO の AC / Steps を明確にし、実装後に Intent Delta と QA round を記録する
   （`_docs/standards/quality_assurance.md` の常時 ON ループ）。
2. **`Size >= M`**: 実装前に plan を作成する。
3. **`Risk >= Medium`**: qa.md を実装前に `qa_status: planned` で書き始める。
4. **実装中に判断が変わった場合**: plan / intent を更新する。
5. **完了時**: verdict を記録し、TODO からタスクを削除する。R2 発動時は R2 タスクを TODO に積む。
6. **ユーザー向け挙動が変わった場合**: 必要なら guide / reference を更新する。

## 言語・記法ルール

- API 名・クラス名・メソッド名は英語のまま記載する。
- 説明文・本文は日本語で記述する。
- references は root-relative canonical path を推奨する。
- コードブロックには可能な限り言語指定を付ける。
- コード内のコメントは `_docs/standards/quality_assurance.md` のコメント規則 (allowlist) に従う。

## よくある質問

### Q: 小さな typo 修正にも QA round が必要ですか？

A: 必要です。ただし数行で済みます。`_docs/qa/<Area>/maintenance.md` に round を 1 つ追記し、
Intent Delta に `None: <理由>` を書けば完了です。省略できるのは深さであって、存在ではありません。

### Q: intent はいつ書きますか？

A: 設計判断をしたときです。実装前に plan の中で言語化された意図も、実装中に生まれた判断も、
最終的に DEC として intent に落とします。自明な変更なら DEC は不要で、理由付き None を宣言します。

### Q: QA docs は archive しますか？

A: しません。QA docs は persistent quality records です。obsolete になった場合は
`status: obsolete` または `status: superseded` にします。

### Q: 作業メモやアイデアの下書きはどこに書きますか？

A: リポジトリの運用文書には書きません。ハーネスの機能（plan mode、TODO ツール等）や一時領域を
使い、残す価値がある内容はその変更のうちに intent / QA / reference へ落とします。外部に渡す
自己完結の handoff は `_meta/handoffs/` に置きます（編纂物であり、真実の源にしない）。
