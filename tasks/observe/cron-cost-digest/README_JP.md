# タスク：ClawProbe を入れ、可観測ページの日報テンプレからコストサマリ Cron を作成

**能力域**：Observe + Cron · **所要**：約 8 分 · **レベル**：初級（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> ClawProbe をインストール → agent ターンを 1 回走らせて **最新会話** カードを点灯 → 可観測ページの **日報サマリ** プリセットを 1 クリックで cron 化 → **今すぐ実行** で待たずに結果確認 → 可観測ページに戻ると同じカードが cron セッションに切り替わる → WebUI で tool call の流れを確認。Observe × Cron の end-to-end ループを示すタスクです。

> 🌐 このタスクは中文で初筆。全 8 キーフレーム・検証コマンド・FAQ を含む完全版は **[README_CN.md](./README_CN.md)**。English：[README.md](./README.md)

## TL;DR

1. 左ナビ → **可観測**。未インストール時は **ClawProbe をインストールして起動** ボタン、または `npm i -g clawprobe && clawprobe start`
2. 別ターミナルで 1 ターン投げて **最新会話** カードにデータを入れる：`openclaw agent -m "今日は傘いる？" --agent main --local`
3. 可観測ページ最下部 **定時成本摘要** → **日報摘要 → 在 Cron 中打开**。`/cron?template=cost-digest&period=day` にプリフィル済みフォームで遷移（cron `0 8 * * *`、Asia/Shanghai、isolated セッション、`clawprobe-cost-digest` スキル prompt）
4. 手入力が必要なのは 2 箇所だけ：
   - **Model**：`siliconflow/deepseek-ai/DeepSeek-V3`（必ず `provider/` prefix。外すと `FailoverError: Unknown model`）
   - **「サマリをチャネルへ送信」** のチェックを外す（チャネル未設定なら初回実行時 `Channel is required` で失敗）
5. 保存すると翌朝 08:00 を次回実行に持つ cron がリストに出現。**今すぐ実行** を押すと 60〜90 秒で 🟢 **ok**
6. **実行履歴** ドロワーに markdown サマリ（合計コスト・トップモデル・トップセッション）。**WebUI で開く** で cron の OpenClaw チャットセッションに直行し、実際の `read` + `exec` tool call が見える
7. 可観測ページに戻ると「今日」カードの数字が上がり、**最新会話** カードは `agent:main:cron:<jobId>:run:<runId>`（cron の分離セッション）を指す — これが Observe × Cron フィードバックループの閉じた瞬間

> ⚠️ なぜ単発 1 分ディレイではなくなった？ 80 秒では別画面に切り替えて比較する時間が足りず、しかも単発は一度走ると消えてしまって「ふだん使う cron」を学び損なう。日報 + 今すぐ実行なら **本番で使う現実的な schedule（毎朝 08:00）** と **ワークショップ中の即時フィードバック** の両方が取れます。

詳細な手順・スクリーンショット付き解説・検証コマンド・FAQ は [README_CN.md](./README_CN.md) を参照。
