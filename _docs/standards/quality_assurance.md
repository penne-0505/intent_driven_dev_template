# Quality Assurance Standard

## 目的

この標準の目的は、実装の「なぜ」(intent) が記録され、参照され、実装がそれを裏切っていないことを
検証可能にすることである。想定読者は人間ではなく coding agent である。人間の役割は標準自体の改訂と、
明示された批准操作に限られる。

QA は「テストを増やすこと」ではない。以下を確認する活動として扱う。

- 実装が intent に記録された設計判断を裏切っていないか。
- 変更の意図 (Intent Delta) が例外なく宣言されているか。
- Acceptance Criteria が自動テスト・手動 QA・validator・review のいずれかで確認されているか。
- 未確認リスクが明示されているか。

設計判断の一次資料は GitHub Issue #11 (意図監査) と、本標準を改訂する commit / PR に残す。

## 三層依存原則

本テンプレートの仕組みは、依存の性質で三層に分類される。

| 層 | 実体 | 依存 | 置いてよいもの |
| --- | --- | --- | --- |
| Tier 0 契約層 | AGENTS.md / `_docs/standards/` / skills (Markdown) | すべての agent が読む | **規範はこの層にのみ置く** |
| Tier 1 検証層 | Deno validator + shell | 環境 (Deno) に依存、ハーネスに非依存 | 機械判定可能な検査 |
| Tier 2 増幅層 | hooks / subagent / headless CLI | ハーネス固有 | 想起・注入・安全ブロックの増幅 |

Tier 2 は「あれば効くが、無くても成立する」ものに限る。規範的な要求を Tier 2 にしか置いてはならない。
理由: ハーネスは多様であり (Claude Code / Codex / その他)、特定ハーネスの機能を前提にした規範は
移植不能な暗黙依存になる。

**単一情報源**: 規範の正典は standards のみとする。skill は手順とトリガーだけを持ち、規範を
復唱せず standards の該当節を参照する。hook の文面は参照 (skill 名・standard 名) に留める。
理由: 復唱は改訂時に古い写しを残し、agent は読んだ方に従うため、ドリフトの発生源になる。

## 常時 ON・深さ可変ループ

すべての変更は、例外なく同じ最小ループを回る。**省略できるのは深さであって、存在ではない。**

```text
TODO (Acceptance Criteria) → 実装 → Intent Delta の宣言 → QA round の記録
```

- **Intent Delta**: この変更が意図の台帳に与える差分。三値のいずれかを必ず宣言する。
  1. `DEC-xxx 新設` — 新しい設計判断を intent に記録した。
  2. `applied: DEC-xxx` — 既存の判断の適用で説明が閉じる。
  3. `None: <理由>` — 判断を要する分岐がなかった。理由のない裸の None は不可。
- 自明な変更に DEC を作る必要はない。禁止されるのは **無言の省略** だけである。理由付き None は
  「検討したが判断はなかった」という反証可能な主張であり、review と後からの監査の対象になる。
- Size / Risk は「文書を書くか否か」ではなく「どれだけ深く書くか」だけを動かす。
  presence が無条件だからこそ、validator は「diff があるのに宣言がない」を意味判断なしに error にできる。
- **ターンは負債を持って終われるが、タスクは負債を持って閉じられない。** 作業ターンの終端で
  ドキュメント未対応があれば、作業を始めずに一言だけ現状を伝え、本筋の次の指示が来たときに
  未対応分を処理する。ただし TODO からタスクを消す時点では QA round と verdict が揃っていなければならない。

### 深さの段階

| 条件 | 追加されるもの |
| --- | --- |
| すべての変更 | Intent Delta + QA round (数行でよい) |
| `Size >= M` | Plan 文書 (`_docs/plan/<Area>/<slug>/plan.md`)。計画行為自体は TODO の AC / Steps で常時行う |
| `Risk >= Medium` | QA 文書を実装前に `qa_status: planned` で書き始める |
| `Risk High / Critical` | rollback / recovery / security / data safety の確認項目、完了前の verdict 必須 |
| DEC 新設 / `Size >= M` / `Risk High` | R2 再構成テストの発動 (後述) |

## Risk 分類

| Risk | 定義 |
| --- | --- |
| Low | 局所的で、失敗しても影響が小さい変更。 |
| Medium | 機能挙動、ワークフロー、validator、ドキュメント規約、agent skill に影響する変更。`Size M` 以上は原則 Medium 以上。 |
| High | 互換性、データ損失、認証、権限、セキュリティ、課金、外部 API、CI/CD、migration に関わる変更。 |
| Critical | 本番障害、secret 漏洩、重大なデータ破壊、ユーザー影響の大きい破壊的変更につながり得る変更。 |

Risk は「作業量」ではなく、失敗時の影響と検証難度で判断する。

### 自動下限 (workflow-sensitive paths)

次のパスに触れる diff は、申告に関わらず `Risk High` として扱う。判定は validator (Tier 1) が行う。

```text
scripts/  .github/  _docs/standards/  .claude/  .codex/  .agents/  AGENTS.md  CLAUDE.md
```

自己申告は残るが、機械は Risk を上げる方向にのみ介入する。理由: ワークフローの根幹を触る変更で
過小申告が起きると、最も検査が必要な変更が最も浅い検査で通るという転倒が生じるため。

## intent decision record (DEC)

intent は「現在の実装を変えるな」と命令する台帳ではない。将来の変更者 (別コンテクストの agent) が、
なぜその実装・省略・境界を選んだのかを理解し、同じ意図を満たす別実装へ安全に変更できるようにする
decision record である。

- DEC の ID は **リポジトリ全体で一意** とする (`intent_schema: 3`)。採番は既存 ID の最大値 + 1。
  一意 ID により、コード側ポインタは ID 単体で解決できる。
- 各 decision entry は最低限、以下を持つ。
  - **What**: 採用した判断。現在値の羅列ではなく、選択した意味や方針を書く。
  - **Why**: 解決したい問題、守りたい性質、避けたい失敗との因果。必須。
  - **Change freedom**: `Why` を保つ限り変更できる実装方式・値・構造。必須。
  - **Why not**: 一見妥当な不採用案と、その案では目的を満たせない理由。実際に検討した場合のみ。
  - **Revisit when**: 判断を再検討できる証拠・環境変化・条件。必要な場合のみ。
- `What` や exact 値を言い換えただけの `Why` は不十分である。値そのものが契約でなければ、
  再計測に基づく調整を `Change freedom` で許容する。

### 何を intent に書き、何を書かないか (知識の 4 分法)

| 種類 | 例 | 行き先 |
| --- | --- | --- |
| why 成分 | 「LINEAR を保つのは base sampling を byte-identical にするため」 | DEC (`Why` / `Change freedom`) |
| 判断履歴 | 日付・採択・変更の経緯 | DEC の更新 / `Revisit when` |
| 純粋な how | 「mip も再生成する」などコードの言い換え | **記録しない** (agent はコードから再構成できる) |
| 耐久的な機構解説 | 繰り返し参照される仕様的説明 (稀) | `_docs/reference/` |

融合した記述の分解判定規則: **「コードを消して書き直したら失われる情報か？」**
失われるなら why (DEC へ)。コードから再導出できるなら how (記録しない)。

## intent-derived invariant (optional)

intent-derived invariant は、active な decision の下で、実装方式が変わっても破ってはいけない結果だけを
表す。すべての decision から INV を作る必要はなく、INV が 0 件でも正常である。
INV の ID もリポジトリ全体で一意とする。

INV へ昇格する前に確認する。

1. 現在のタスク完了後に破られても、active decision の下でなお誤りである。
2. 契約上固定する理由のない exact 値、比較 variant、実験の統制条件、migration 中だけの保全条件ではない。
3. 別実装でも守るべき結果として書ける。
4. 親となる `DEC-xxx` の `Why` から因果的に導ける。

一つでも満たさなければ INV にせず、`Change freedom` / Acceptance Criteria に置く。INV を作った場合だけ、
自動テスト・validator・手動 QA・diff review のいずれかへ QA 文書の確認手段で割り当てる。
exact 値を固定するテストは、その値自体が契約である理由を decision が説明している場合だけ作る。

## intent ↔ code traceability

コードから intent へ到達する経路は **ポインタコメントのみ** とする。

### コメント規則 (allowlist)

コード内のコメントは、以下の形式だけが許可される。散文コメントは validator が error にする。

```text
// intent: DEC-042 — この実装・省略が必要な理由の一行要約
// intent-invariant: INV-007 — active decision 下で破れない結果
// Covers AC-001 / INV-002   (テストコード内の対応付け)
```

- 機械向けコメントは許可される: shebang、pragma 類 (`@ts-expect-error`, `biome-ignore`,
  `eslint-disable`, `deno-lint-ignore` 等)、プロジェクトが必要とする license header。
- TODO コメントは禁止する。作業台帳は `TODO.md` のみである。
- doc comment (JSDoc / docstring) は既定で禁止。ライブラリとして API 文書を配布する
  プロジェクトのみ、validator 設定でシンタックス単位の許可を宣言できる。
- ダッシュ以降は decision の `What` や値を繰り返さず、因果を一行で要約する。

この規則の理由: agent が編集時に確実に読むのは編集対象ファイルだけであり、ファイル内ポインタは
「ここに判断がある」という存在の通知を担える唯一の機構である。散文を禁止することで、
コメントの存在自体が判断の信号になり、why の記録先は intent に一本化される (Intent Delta の
宣言義務と対をなす)。書きたい散文があるなら、それは DEC に書くべき why か、捨ててよい how である。

### 置き忘れ (coverage)

ポインタの置き忘れは機械判定できない。R1 / R2 review が「この diff に、アンカーすべき判断が
埋まっていないか」を確認する。

## QA 文書 (qa.md)

QA の計画と検証記録は、slug ごとの単一文書 `_docs/qa/<Area>/<slug>/qa.md` に統合する
(`qa_schema: 4`)。計画は `qa_status: planned` で始まり、検証を経て `verified` 等へ遷移する。

```text
--- frontmatter: qa_status / risk / qa_schema: 4 ---
## Acceptance Criteria      … AC-001 形式。TODO と同期
## Checks                   … 各 AC / 適用 INV をどう確認するかの表。実装前に書く
## Rounds                   … 追記専用。実施のたびに 1 round 足す
```

各 round は以下を持つ。

- 実行したコマンドと結果 (実行していないコマンドを書かない)
- AC の充足状況
- **Intent Delta** (DEC 新設 / applied / 理由付き None)
- **R2** (発動条件を満たす場合のみ: verdict / PENDING / gap)
- **Transferable Principles** (candidate または理由付き None)
- **Verdict** (`PASS` / `PARTIAL` / `FAIL` / `BLOCKED`)

- Rounds は追記専用である。過去の round (特に planned の Checks) を結果に合わせて書き換えない。
- 微小変更 (Intent Delta が applied / None のみ) は、slug を切らず
  `_docs/qa/<Area>/maintenance.md` へ round を追記する。書式は同一で、置き場だけが異なる。
- QA 文書はテストコードの置き場ではない。実行可能なテストはコードベース側の標準的な場所に置く。
- QA 文書は persistent quality records であり archive しない。obsolete になったら
  `status: obsolete` / `status: superseded` にする。

### verdict

| Verdict | 意味 | qa_status |
| --- | --- | --- |
| `PASS` | AC と該当 INV が確認され、完了扱いにできる | `verified` |
| `PARTIAL` | 一部未確認だが、残リスクと follow-up TODO が明示され、限定的に完了扱いにできる | `partial` |
| `FAIL` | 必須条件を満たしていない。完了不可 | `failed` |
| `BLOCKED` | 外部要因により検証不能。完了不可 | `blocked` |

`PARTIAL` / `FAIL` / `BLOCKED` では、残リスクと次アクションを必須にする。
frontmatter の `qa_status` は本文の `Verdict` と一致させる。

## review (R1 / R2)

内容の質は validator では担保できない。review は二つの機能に分割する。

### R1: 妥当性 review

変更は正しいか、影響する DEC の `Why` と `Change freedom` に沿うか、`None` の理由は妥当か、
ポインタの置き忘れはないかを確認する。実装した agent 自身が review の観点に切り替えて行ってよい。
学習基盤の異なる別モデルが利用できる環境では、そちらに依頼することが上位互換である
(同一モデルのコンテクスト分離は視点を変えない)。

### R2: 再構成テスト

**「diff とリポジトリ内の docs だけから、この変更の why を再構成できるか」** を検査する。
これは成果物の十分性の統合テストであり、実装した本人には原理的に実行できない
(欠けている知識を自分のコンテクストが補ってしまうため)。

- 発動条件: Intent Delta が DEC 新設 / `Size >= M` / `Risk High` のいずれか。
- 普遍形 (Tier 0): 変更を閉じるとき QA round に `R2: PENDING` を書き、`TODO.md` に R2 タスクを積む。
  **次のセッション** (実装時の文脈を自然に持たない agent) がそのタスクを拾い、以下の固定設問に答える。

```text
あなたはこのリポジトリを初めて見る。与えられた diff と、リポジトリ内の docs・コードだけを読んで答えよ:
1. この変更は何を達成しようとしたか (why を再構成せよ)
2. 根拠となる DEC はどれか。その Why / Change freedom はこの diff を説明しきるか
3. この変更を壊さずに拡張するには何を守る必要があるか。その根拠は docs にあったか、推測で補ったか
4. why を再構成できなかった diff 箇所を列挙せよ
```

- 結果と gap を QA round に追記する。gap (再構成できなかった箇所・推測で補われた箇所) は
  DEC 修繕タスクとして `TODO.md` に積む。
- 同期形 (Tier 2、任意): subagent や headless CLI を持つハーネスでは、同じ固定設問を隔離された
  呼び出しに渡して completion 前に実行してよい。この場合、実装 agent が正解 (真の why) と
  突き合わせて採点できるため、より強い形になる。固定設問に実装セッションの文脈を追記してはならない。
- validator は R2 フィールドの presence と PENDING の滞留を検査する。中身の質は R2 自体が判定する。

## transferable principle (教訓の昇格)

特定の feature に閉じない一般則 (transferable principle) は、以後のすべての agent の判断を変える、
このシステムで最も影響半径の大きい書き込みである。

- agent は作業終了時に「このセッション限りではない学びがあるか」を必ず確認し、QA round に
  candidate または理由付き `None:` を記録する。裸の `None` は不可。
- candidate は `_docs/intent/<Area>/conventions/decision.md` へ **candidate マーク付きの DEC**
  として直接追記してよい。candidate マークは「未批准であり、規則として扱ってはならない」を意味する。
- **規範化は user がマークを外す明示操作のみ**で行われる。agent が自分でマークを外してはならない。
  validator は candidate 状態の DEC を参照するコード内ポインタを error にする
  (未批准原則の実効化を防ぐ)。
- candidate を記録した agent は、最終報告で必ず定型提示を行う:
  1. 原則の本文 (1–2 行) / 2. 出所 (何がきっかけか) / 3. 採用時の影響範囲 /
  4. 採用のリスク・反例になりそうなケース / 5. agent 自身の推奨と理由。
- docs-inventory と validator は未批准 candidate の件数と経過日数を warning として報告する
  (督促するがブロックしない)。

## schema 移行

新規文書は `intent_schema: 3` / `qa_schema: 4` を使う。旧 schema の文書は **見える未完了** として扱う。

- 一斉手動移行は要求しない。意味を変更する編集を行う文書から、順に新 schema へ移行する。
- validator と docs-inventory は旧 schema 文書の残数を warning として常時報告する。
- テンプレート更新 (docs-template-migration) の際は、残存する旧 schema 文書の移行計画を
  migration 計画に含める。
- 日付による期限は設けない。
