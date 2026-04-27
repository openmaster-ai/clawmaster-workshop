# タスク：SkillGuard で「クリーン」と「リスクあり」のスキルを比較スキャン

**能力域**：Guard · **所要**：約 6 分 · **レベル**：初級（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> ClawMaster の **技能** ページで、ローカルにインストール済みの 2 つの skill を並べてスキャン：`linkedin-content`（A 級 · 0 findings）と `baoyu-url-to-markdown`（C 級 · 7 findings — HIGH `exec(` 2 件、MEDIUM `innerHTML =` 4 件、LOW `allowed-tools` 未宣言 1 件）。ワンクリック比較で、SkillGuard が何を見ていて、なぜコミュニティ skill をそのまま agent に渡すのが危険なのかを理解します。

> 🌐 このタスクは中文で初筆。全 3 キーフレーム・CLI 検証・静的解析の限界に関する FAQ を含む完全版は **[README_CN.md](./README_CN.md)**。English：[README.md](./README.md)

## TL;DR

1. 左ナビ → **技能**。フィルタに `linkedin-content` → **扫描** をクリック。初回は npm から `@clawmaster/skillguard-cli` を取得するので 30〜60 秒かかる。完了すると **A · 未発見問題 · 安全分 0**
2. A パネルの **查看详情** をクリック — 同じ drawer コンポーネントで 4 サマリタイルは `A · 安全 / 安全分 0 / 問題数 0 / 最高重大度 低危`（「低危」は基準プレースホルダで実際の LOW finding ではない）、本体は緑の **未発見問題** バッジ、右サイドバーは `严重 0 · 高危 0`。A がどう見えるかを覚えておくのが比較の前提
3. フィルタを `baoyu-url-to-markdown` に変え **扫描** → アンバーに：**C · 問題数 7 · 高危 2 · 安全分 42**。**查看详情** で同じ drawer を開くと、主要发现 エリアに MEDIUM `innerHTML =` が 3 件（ファイルパス + 行番号 + 中英両言語の修复建议）、右サイドバーは `严重 0 · 高危 2 · MEDIUM 4`
4. 全 7 件（drawer は top 3 のみ表示）を見たい場合は CLI：`npm exec --yes @clawmaster/skillguard-cli -- ~/.openclaw/skills/baoyu-url-to-markdown --json`

## なぜこの 2 つか

- **linkedin-content** は markdown + frontmatter だけ（執筆スタイルガイド、コードなし）。`allowed-tools` が宣言されているか、そもそも agent ツールを呼ばない場合は「最小権限」チェックもパス — これが A 級の正しい姿
- **baoyu-url-to-markdown** は headless Chrome を CDP 駆動し、Defuddle で HTML をクリーニングして markdown に変換する。この形だと `innerHTML =`（DOM スクレイピング）や `exec(`（アダプタ dispatch）が正当に必要になる — Guard の目的は「この skill が悪い」と言うことではなく、**agent のデフォルトツール一覧に入れる前に、その skill が触っている API を把握しておく** こと

A と C がワンクリックで対比できるのがこのデモのポイント。

## SkillGuard がカバーする / しない範囲

静的解析（パターンルール）でカバー：

- **Code Security（CWE / OWASP）**: `eval(`、`new Function`、`child_process.exec`、`innerHTML =`、安全でない動的 import
- **最小権限（Google SAIF）**: `allowed-tools` frontmatter 未宣言 → skill はデフォルトで全ツール使用可
- Token バジェット見積（L1/L2-eager/L2-lazy/L3）で skill がロード時にコンテキストを何トークン食うか可視化

**カバーできない** こと（skill をユーザーに渡す際はドキュメント化を）：

- 実行時に組み立てられるコード（`new Function(<難読化>)`）、ネットワークから取得したペイロード、skill が shell-out する外部バイナリ内のロジック（例：`baoyu-fetch CLI` 本体）
- これらを防ぐには OpenClaw の `allowed-tools` + exec-policy サンドボックス + ClawMaster のチャネル承認を併用 — SkillGuard は最初のゲートであり、唯一のゲートではない

詳細（スクリーンショット付きの 3 ステップ・直接 `curl /api/skills/scan` による検証・drawer が top 3 のみ表示する理由と 7 件全量を CLI で得る方法・初回 npm pull でハングした場合の FAQ・CI への組み込み）は [README_CN.md](./README_CN.md) を参照。
