# Case: legacy-verification-append

## Scenario

長期運用中の repo で、対象 feature の verification が template v1.3.0 以前に作られた legacy (`qa_schema: 2`) 文書として既に存在する。agent は新しい検証 round をこの既存文書へ append する。この経路では「New verification documents use `qa_schema: 3`」の規則が字義上は適用されず、`Transferable Principles` の記録先が summary へ逃げて session とともに蒸発しやすい (v1.3.0 の実運用初日に観測された failure mode)。

## Initial State

- `_docs/qa/<Area>/<slug>/verification.md` が `qa_schema: 2` で存在し、過去の検証 round を複数含む。
- 対応する intent / test-plan は存在し、TODO は `Risk >= Medium`。
- 運用は PR を経由せず main へ直接 commit している (summary / PR description は恒久記録として機能しない)。

## Agent Task

実装変更を行い、`qa-review` / `post-implementation` を経て新しい検証 round を既存 verification へ append し、タスクを完了扱いにできるか判断する。

## Expected Documents Touched

- `_docs/qa/<Area>/<slug>/verification.md` — 新 round の append と**同じ編集内で** `qa_schema: 3` へ移行し、`Transferable Principles` を含む不足 section を追加する。

## Expected QA Behavior

- legacy schema を理由に reflection を summary へ逃がさない。既存 verification がある限り、記録先は常に verification である。
- `Transferable Principles` に candidate (1–3 行) または理由付き `None:` を残す。
- リンクや typo の修正だけの編集では schema 移行を強制しない (意味を追加する編集のみが移行 trigger)。

## Expected Decision / Invariant Behavior

- schema 移行は新 round の内容に対する編集であり、過去 round の記述を書き換えない。
- 昇格判断は user に委ねる。cross-cutting な candidate の昇格先は `_docs/intent/<Area>/conventions/decision.md`。

## Expected Verification Behavior

- 移行後の文書は `qa_schema: 3` の必須 section をすべて持ち、`validate-qa` が通る。
- verdict と `qa_status` の一致など、既存の verification 契約は従来通り満たす。

## Expected TODO.md Behavior

- `qa_schema: 2` のまま新 round だけ append した状態でタスクを完了扱いにしない。

## Expected Test / Validator Behavior

- `deno run --allow-read --allow-env --allow-run=git scripts/validate-qa.ts` が移行後文書の presence を検証する。
- validator に「append event の検出」を追加しようとしない。静的検証では v1.3.0 以前の正当な複数 round と区別できないため、この規則の強制は skill と review の領分である。

## Failure Modes to Watch

- 「文書が v2 だから TP section は不要」と字義解釈し、reflection を summary にだけ書いて蒸発させる。
- schema 移行を怠り、v2 文書の末尾に round を積み続ける。
- 逆振れ: リンク修正だけの編集で不要な schema 移行や過去 round の改変を行う。
- 移行時に過去 round の内容を「整理」と称して書き換え、検証証跡の履歴性を壊す。
