# タスク：`package-download-tracker` の時系列ストアとして PowerMem を使い、週次パッケージダウンロード cron を作成

**能力域**：Save + Cron · **所要**：約 12 分 · **レベル**：中級（[cron-cost-digest](../../observe/cron-cost-digest/) と [powermem-takeover-file-memory](../powermem-takeover-file-memory/) 完了後）

> スキルカタログで bundled の `package-download-tracker` を確認 → URL を貼ってプリフィル済みの cron フォームを開く → **今すぐ実行** でスキルに npm / PyPI を叩かせて PowerMem に観測を書き込ませる → WebUI で tool call の流れを検証 → `/memory` ページに 3 件の新エントリが現れる。PowerMem を「事実インデックス」から「定期スキルの時系列バックエンド」に格上げ — ただし **「前週」列が実数値で埋まるのは来週以降**。今日はそのための種をまき、記憶召回経路が繋がっていることを検証する、というのが本タスクの正しい叙事。

> 🌐 このタスクは中文で初筆。全 6 キーフレーム・検証コマンド・FAQ を含む完全版は **[README_CN.md](./README_CN.md)**。English：[README.md](./README.md)

## TL;DR

1. 左ナビ → **スキル** → *当前工作区已安装* までスクロールし **`package-download-tracker`** カードを確認。カード説明には npm + PyPI、週次/月次、stored history observations の記述。**`/cron` ページ側には cron テンプレカードは存在しない** — エントリは URL 経由のみ。
2. `http://localhost:16223/cron?template=package-downloads&period=week` をアドレスバーに貼る。テンプレ読み込みの toast が出て **定時任務新規作成** モーダルがプリフィル済み状態で開く：
   - 名前 / 説明 / メッセージ（サンプル npm `clawmaster, powermem` + PyPI `powermem` 付きの完全 skill prompt）
   - cron `0 8 * * *`、`Asia/Shanghai`、isolated セッション、agent `main`
   - **手入力は 2 箇所のみ：**
     - **モデル**：`siliconflow/deepseek-ai/DeepSeek-V3`（`provider/` プレフィクス必須、外すと `FailoverError: Unknown model`）
     - **メッセージ**：追跡対象をサンプルから置き換えて OK。ただし `npm packages \`...\`; PyPI packages \`...\`` の構造、および「履歴優先・メモリ/キャッシュ内部機構を出力に出さない・数値を捏造しない」制約はそのまま残す
3. 保存 → **今すぐ実行**。約 30 秒で 🟢 **ok**。実行履歴ドロワーは timing のみ表示。実際のマークダウン回答は **在 WebUI 中打开** で OpenClaw chat view に遷移して確認
4. WebUI chat 最終 Tool ロール応答の表は *Current week / Previous week / Change* の 3 列構成。期待値：
   - npm パッケージ：`Previous week = n/a`、変化列は「前週データなし」（コールドスタート、PowerMem に履歴なし）
   - PyPI パッケージ：`Previous week` に実数値 + 実パーセンテージ — **これは PowerMem 由来ではない**。pypistats `/recent` エンドポイント自身が前週ウィンドウを返してくる
5. 左ナビ **記憶** / または `openclaw ltm list --json | grep 'package-download-tracker registry:'` で確認。3 件の `reference` 形式エントリが新規追加されている。各エントリは平文で `package-download-tracker registry:<r> package:<p> period:week` + `summary: ...` + `snapshot-json: {...}` の 3 行。`snapshot-json:` マーカーが次回実行の `--load-memory` grep で使われる
6. **同日中に**もう一度 **今すぐ実行**：表は前回と完全に同じ — npm は `n/a` のまま、PyPI も同じ差分。これは設計通りの挙動。PowerMem に保存された今日分の観測は `fetchedAt` が今日の日付 → スキルにとっては「当週ウィンドウに重なっている」ため「前週ベースライン」としてカウントされない。真の *PowerMem-as-time-series* の効果は `fetchedAt` が前の暦週に収まった時に初めて現れる — つまり明日以降の 08:00 定時実行。今日は種まき
7. WebUI の tool call タイムラインを検証：
   - 正常路：`session_status → sessions_spawn → sessions_yield` — エージェントがサブエージェントを spawn し、その中で `read SKILL.md` + `exec track-downloads.mjs --load-memory --save-memory` が実行される
   - 異常路：`Tool package-download-tracker not found` — エージェントがスキルを built-in ツールとして直接呼ぼうとした（スキルはツールではなく `read` + `exec` の手順書）。この場合スキルは再実行されず、回答は `<relevant-memories>` コンテキストからの組み立てで PowerMem に新エントリは書かれない。診断：実行前後で `package-download-tracker registry:` 件数を比較、増えていなければスキル未実行

> ⚠️ なぜ `save/` 配下で `observe/` ではないか：`clawprobe-cost-digest` は毎回 SQLite を読み直してマークダウンを再生成する、記憶無関係のスキル。`package-download-tracker` は PowerMem を永続時系列ストアとして使うのが本質的な新規性で、既存 cost-digest cron との差分はまさにそこ。よって Save に置く。

詳細な 6 ステップ手順・スクリーンショット付き解説・`jq` / CLI クロス検証、コールドスタート 0 / 同日再実行で前週が埋まらない理由 / gateway 自動キャプチャ汚染（スキル側は `snapshot-json:` マーカーで弾くため履歴召回には影響しないが PowerMem UI 上では見える）の掃除 incantation / tool not found 退行の診断 / `--period day` が使えない件などの FAQ は [README_CN.md](./README_CN.md) を参照。
