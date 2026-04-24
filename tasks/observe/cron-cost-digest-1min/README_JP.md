# タスク：Observe 有効化 + 1 分後に ClawProbe コスト サマリを取得する単発 Cron

**能力域**：Observe（コア能力 #2）+ Setup（cron サブモジュール）
**所要時間**：約 8 分
**レベル**：初級（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> Observe モジュールを有効化し、GUI のスケジュール エディタで **単発** cron ジョブを作成し、1 分後に `clawprobe-cost-digest` スキルを呼び出して当日のモデル コスト サマリを実行履歴に残します。

> 🌐 本タスクは中国語先行執筆です。スクリーンショット付きの完全版は **[README_CN.md](./README_CN.md)**。English: [README.md](./README.md)

## 手順サマリ

1. 左ナビ → **可观测**。空の場合は「安装并启动 ClawProbe」をクリック（または `npm i -g clawprobe && clawprobe start`）
2. 左ナビ → **定时任务** → `+ 新建任务`
3. 名称：`1 分钟后的成本快照` · 调度类型：**单次任务** · 运行时间：ISO タイムスタンプ（現在 +80 秒）を `python3 -c "import datetime as d; t=d.datetime.now().astimezone()+d.timedelta(seconds=80); print(t.isoformat(timespec='seconds'))"` で生成して貼り付け
4. 消息：`clawprobe-cost-digest` スキルのプロンプトを貼り付け（正確な文字列は中国語版参照）
5. 保存。約 1 分待機。発火後は cron カードの **「在 WebUI 中打开」** をクリック — 対応する cron セッションに直接ディープリンクし、ツール コールと最終サマリの両方を表示します。**运行记录** 抽屉は同じ stdout を省スペースで表示、`curl /api/clawprobe/cost?period=day` で数値をクロスチェックできます。

## ポイント

ClawMaster は受動的可視化（Observe が ClawProbe の SQLite を読む）と能動的自動化（Cron がスキルをスケジュール実行）を分離しています。**cron → skill → ClawProbe → 実行ログ** のつなぎ込みは、定期レポート、異常検知、スケジュールクリーンアップの標準パターンです。

全 5 キーフレーム、CLI 検証（`curl /api/clawprobe/cost?period=day`）、後続タスク、空出力の対処、ゲートウェイ依存、6 フィールド cron 式のヒントを含む完全版は [README_CN.md](./README_CN.md) を参照してください。
