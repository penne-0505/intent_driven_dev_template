# ドキュメント運用標準

## 目的

- `_docs/` 配下のドキュメント種別ごとの役割と運用境界を明確化する。
- intent を恒久的な設計判断ログとして扱い、QA 文書へ接続する。
- すべての変更が常時 ON・深さ可変ループ (`_docs/standards/quality_assurance.md`) を回れるよう、
  記録先を一意に保つ。

## 適用範囲

- 対象: `_docs/plan/`, `_docs/intent/`, `_docs/qa/`, `_docs/guide/`, `_docs/reference/`, `_docs/archives/`。
- 非対象: アプリケーションコードや API の実装規約。

## ディレクトリの役割

| パス | 目的 | 備考 |
| --- | --- | --- |
| `_docs/plan/<Area>/<slug>/plan.md` | `Size >= M` の変更の実施計画 | intent に昇華する原典。タスク完了後に archives へ移送できる。 |
| `_docs/intent/<Area>/<slug>/decision.md` | 設計判断 (DEC) の恒久記録 | archive しない。 |
| `_docs/intent/<Area>/conventions/decision.md` | 特定 feature に閉じない原則 (transferable principle) の集約 | candidate マーク付き DEC の置き場を兼ねる。 |
| `_docs/qa/<Area>/<slug>/qa.md` | QA の計画と検証記録の単一文書 | `qa_status` がライフサイクルを表す。archive しない。 |
| `_docs/qa/<Area>/maintenance.md` | 微小変更の QA round 集約 | 書式は qa.md の Rounds と同一。archive しない。 |
| `_docs/guide/<Area>/<slug>/usage.md` | 実装済み機能の運用ガイド | 必要になったときに作る。恒久保守義務はない。 |
| `_docs/reference/<Area>/<slug>/reference.md` | 詳細リファレンス・耐久的な機構解説 | 必要になったときに作る。恒久保守義務はない。 |
| `_docs/archives/plan/<Area>/<slug>/...` | 完了タスクの plan の保管庫 | archive 対象は plan のみ。 |
| `_meta/` | 検証素材・handoff・非運用資料の圏外領域 | agent への指示として読まない。 |

`<Area>` は `TODO.md` の `Area` と一致させる。`<slug>` は機能・変更単位の kebab-case 名にする。
draft / survey ディレクトリは存在しない。作業メモが必要な場合はハーネスの機能や一時領域を使い、
残す価値のある内容は intent / QA / reference のいずれかへ、その変更のうちに落とす。

## Canonical Paths

```text
_docs/plan/<Area>/<slug>/plan.md
_docs/intent/<Area>/<slug>/decision.md
_docs/intent/<Area>/conventions/decision.md
_docs/qa/<Area>/<slug>/qa.md
_docs/qa/<Area>/maintenance.md
_docs/guide/<Area>/<slug>/usage.md
_docs/reference/<Area>/<slug>/reference.md
_docs/archives/plan/<Area>/<slug>/plan.md
```

references は root-relative canonical path を推奨する。

## ライフサイクル

1. **すべての変更**: `TODO (AC) → 実装 → Intent Delta の宣言 → QA round の記録`。
   深さの段階は `_docs/standards/quality_assurance.md` に従う。
2. **`Size >= M`**: 実装前に plan を作る。plan は合意済み仕様・実施計画の単一参照点であり、
   意図成分 (why) は最終的に intent の DEC へ昇華する。
3. **`Risk >= Medium`**: qa.md を実装前に `qa_status: planned` で書き始める。
4. **plan の archive**: タスク完了後、対応する intent が存在することを確認し (常時 ON ループでは
   構成上必ず存在する)、`_docs/archives/plan/` へ `git mv` で移送し、参照リンクを更新する。
5. **guide / reference**: 利用者向けの説明や耐久的な機構解説が必要になったときに作成・更新する。
   作らないことは違反ではない。

## handoff 文書

このマシンやリポジトリにアクセスできない相手 (外部モデルによる review など) へ渡す自己完結の
文書は `_meta/handoffs/` に置く。

- handoff は記録ではなく **export (編纂物)** である。真実の源は repo 内の docs であり続ける。
- **純編纂規範**: handoff を書いている最中に新しい判断や why が生まれたら、先に intent / QA へ
  記録してから handoff に写す。handoff にしか書かれていない知識は、ループ文書の欠陥の兆候である。

## 破壊的操作

- `rm` / `git rm` による恒久削除は禁止する。不要に見えるファイルでも、削除はユーザーに提案して
  実行を待つ。
- plan の archive 移送に限り `mv` / `git mv` を使う。移送は削除ではなく履歴保持のための移動である。
- `_docs/intent/**` / `_docs/qa/**` / `_docs/guide/**` / `_docs/reference/**` を archives へ
  移動しない。obsolete になった文書は `status: obsolete` / `status: superseded` にする。

## テンプレート repo 自身の meta-work に対する例外

本 repo は intent-driven development のテンプレートとして配布される。テンプレート利用者は
新規プロジェクトとしてこの repo を起点に作業するため、**テンプレート repo 自身の改善作業
(meta-work) に伴って生成された intent / plan / qa docs** を配布物に混入させない運用を許容する。

- 対象: テンプレート repo そのものを磨くための作業として作成された intent / plan / qa。
- 分類上の扱い: 上記対象は persistent records の射程外とする。決定事項が `_docs/standards/` へ
  吸収された後は、ライフサイクル上「保持義務のあるドキュメント」とは見なさない。テンプレ repo に
  対しては git 履歴と GitHub Issue / PR がその役割を担う。
- 操作の権限: 本例外は分類の整理であり、「破壊的操作」の原則を上書きしない。
- 適用しない範囲: 利用者プロジェクトとして clone した後の通常運用には適用しない。

## Root Markdown と一回限り prompt

- root 直下の Markdown は、coding agent に active project guidance として読まれる前提で管理する。
- 一回限りの implementation prompt は root に残さない。履歴として残す場合は `_meta/prompts/` 等の
  明確に非運用の場所へ移し、ファイル先頭に historical / non-operational warning を付ける。
- 現在の作業指示は `AGENTS.md`、`TODO.md`、`_docs/documentation_guide.md`、`_docs/standards/`、
  関連 Skills を参照する。

## TODO.md 完了処理

- 完了タスクは `TODO.md` から削除する。
- 完了履歴の正典は QA round (qa.md / maintenance.md) である。PR・commit は QA round への
  ポインタとして機能する。
- `TODO.md` に Done / Archived セクションを作らない。
- follow-up が必要な場合は、新しい ID を採番して Backlog に追加する。
- タスク削除の前提: QA round と verdict が存在し、verdict が `PASS`、または残リスクと follow-up
  が明示された `PARTIAL` であること。`FAIL` / `BLOCKED` は完了扱いにしない。
- R2 が発動したタスクは、`R2: PENDING` を QA round に記録し、R2 タスクを `TODO.md` に積んで
  いれば削除できる (R2 は次セッションの作業として残る)。

## Front-matter Schema

`_docs/standards/` 配下を除く運用対象ドキュメントは、以下の共通 front-matter を持つ。

| フィールド | 説明 |
| --- | --- |
| `title` | 文書タイトル |
| `status` | `proposed` \| `active` \| `superseded` \| `obsolete` |
| `created_at` | `YYYY-MM-DD` |
| `updated_at` | `YYYY-MM-DD` |
| `references` | 関連リンク配列。root-relative canonical path を推奨。 |
| `related_issues` | 関連 Issue の番号配列。ない場合は `[]`。 |
| `related_prs` | 関連 PR の番号配列。ない場合は `[]`。 |

`_docs/qa/**/*.md` は、共通 front-matter に加えて以下を必須とする。

| フィールド | 説明 |
| --- | --- |
| `qa_status` | `planned` \| `in-progress` \| `verified` \| `partial` \| `failed` \| `blocked` |
| `risk` | `Low` \| `Medium` \| `High` \| `Critical` |

新しい schema を使う文書は、次の marker を持つ。

| 対象 | フィールド | 値 (新規作成時) |
| --- | --- | --- |
| `_docs/intent/**/*.md` | `intent_schema` | `3` |
| `_docs/qa/**/*.md` | `qa_schema` | `4` |

marker のない文書、および旧番号の文書は **見える未完了** として受理する: validator と
docs-inventory が残数を warning で報告し続け、意味を変更する編集の際に新 schema へ移行する。
一斉手動移行は要求しない。日付期限は設けない。詳細は `_docs/standards/quality_assurance.md` の
schema 移行を参照。

## Template revision provenance

この template を適用した project が後続 release を継続的に取り込めるよう、upstream template の
provenance を `docs-template.lock.json` に記録する。雛形は root の `docs-template.lock.example.json`
とする。

```json
{
  "schema": 1,
  "source": "https://github.com/penne-0505/intent_driven_dev_template.git",
  "revision": {
    "tag": "v1.2.0",
    "commit": "<tagが解決するfull 40-character commit SHA>"
  }
}
```

- **更新単位**: upstream が推奨する immutable release tag を使う。branch 名や moving tip を lock に
  記録しない。
- **実体の固定**: tag 名だけでなく、その tag が解決する full commit SHA を記録する。後から同名 tag の
  解決先が変わった場合は migration を停止する。
- **初回導入**: tagged release から開始した project は、雛形を `docs-template.lock.json` へコピーし、
  採用 tag の full SHA を記録する。lock は project の tracked file とする。
- **通常更新**: lock の revision を `B`、次の推奨 tag を `U` とし、`docs-template-migration` skill で
  project customization を含む three-way migration を行う。`U` の配布ファイルを reconciliation し、
  compatibility checks が成功した後に、lock を最後の migration write として `U` へ進める。
  closure verification では更新後の tag と full SHA を確認する。
- **schema 状態との分離**: lock は統合済み upstream revision だけを示す。strict schema migration の
  完了・延期・残リスクは QA round に記録し、lock schema へ混在させない。
- **pre-v1.0.0 bootstrap**: tag、lock、local migration skill がない既存 project は、repository
  history、導入記録、upstream と一致する blob から最後に採用した commit `B` を復元する。project
  固有ルールを安全境界とし、対象 `U` の skill を外部入力としてレビューしてから書き込みを行う。
  `v1.0.0` を中継せず、`v1.0.0` 以降の任意の推奨 tag へ直接移行できる。`B` が一意に特定できない
  場合は owner 判断を推測せず停止する。compatibility migration の PASS 後に初回 lock を `U` で
  作成する。
- **template release 側**: release tag を作成する commit では、`docs-template.lock.example.json` の
  `revision.tag` をその tag 名へ更新しておく。commit SHA は tag 作成後に解決するため、雛形では
  placeholder のままとする。

`DD_SCOPE_BASE` は導入先 repository 内の validator 対象を決める project-local git ref であり、
upstream template の採用 revision を示す値ではない。両者を兼用しない。

## 段階的導入スコープ (Incremental Adoption)

既存プロジェクトへ後付け導入する際、テンプレート規約に従っていない既存 docs / コードが一斉に
検証対象となり CI が埋まるのを避けるため、validator は「導入以降に追加・変更されたファイル」だけを
判定対象に絞る opt-in スコープ機構を持つ。本節を段階的導入スコープの正典とする。

- **既定は全走査**: 環境変数が未設定なら、各 validator は従来通り全対象を走査する。テンプレート
  自身の CI はこの既定で dogfooding を続ける。
- **`DD_SCOPE_BASE`**: 導入時点の git ref (commit / tag) を設定すると、
  `git diff --name-only --diff-filter=A <ref>...HEAD` で得た「追加されたファイル」のみを判定対象に
  する。
- **`DD_SCOPE_DIFF_FILTER`**: `DD_SCOPE_BASE` 使用時の git `--diff-filter` を上書きする。既定は
  `A`。既存ファイルを編集した時点で管理対象にしたい導入先では `ACMR` を設定する。
- **`DD_SCOPE_PATHS`**: 改行 / コロン区切りの明示パスリスト。優先順位は
  `DD_SCOPE_PATHS > DD_SCOPE_BASE > 未設定`。
- **`TODO.md` は常時検証**: `validate-todo.ts` はスコープの影響を受けない。
- **横断チェックの扱い**: リンク / references の整合チェックは判定の起点ファイルだけをスコープで
  絞り、参照先の存在確認はファイルシステム全体に対して行う。
- **必要権限**: スコープ対応 validator の実行には `--allow-env` を、`DD_SCOPE_BASE` (git) 使用時は
  加えて `--allow-run=git` を付与する。権限が無い場合は安全側 (全走査) へフォールバックする。
- **CI 設定**: `DD_SCOPE_BASE` を使う場合、baseline commit を参照できるよう `actions/checkout` で
  `fetch-depth: 0` を設定する。

既存コード・仕様書・過去の記録から intent を掘り起こす導入時の手順 (意図採掘) は、本標準の
射程外であり、独立した手順書として整備する。

## コンプライアンス

- ドキュメントに秘密情報・個人情報を含めない。環境値は `.env.example` を参照する。
- CI ログ出力にはマスク設定を適用し、機密情報が残らないようにする。
- intent と QA docs を archive しない。
- Deno validator をローカルと CI で実行する。ローカル検証の正典は `./scripts/check-docs.sh` とする。
