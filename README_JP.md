<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/dark/horizontal.png" />
    <img src="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/white/horizontal.png" width="100%" alt="ClawMaster" />
  </picture>
</p>

<p align="center"><strong>ClawMaster · ワークショップ</strong></p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster"><img src="https://img.shields.io/badge/%F0%9F%A6%9E%20Main%20repo-ClawMaster-111111?style=for-the-badge" alt="Main repo: ClawMaster" /></a>
  <img src="https://img.shields.io/badge/You%20are%20here-Workshop-0A7EA4?style=for-the-badge" alt="You are here: Workshop" />
</p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster/releases"><img src="https://img.shields.io/github/v/release/openmaster-ai/clawmaster?label=Tracks%20ClawMaster&color=F5A623&style=for-the-badge" alt="Tracks ClawMaster latest release" /></a>
  <a href="https://github.com/openmaster-ai/clawmaster/milestone/1"><img src="https://img.shields.io/badge/Next%20milestone-v0.4.0-6f42c1?style=for-the-badge" alt="Next milestone: v0.4.0" /></a>
</p>

ClawMaster + OpenClaw の実運用シナリオで手を動かす Lab（実験）と Task（タスク）を集めた学習リポジトリ。メインリポジトリは [openmaster-ai/clawmaster](https://github.com/openmaster-ai/clawmaster)、こちらは手を動かす副読本です。

> 🌐 中文（デフォルト）：[README.md](./README.md) · English：[README_EN.md](./README_EN.md)
>
> 初回オフライン開催は**中国・深圳**のため、リポジトリのデフォルト言語は中文です。EN / JP 版は他地域の読者が開催予定とタスクインデックスを参照するための副本です。

## リポジトリ構成

```
clawmaster-workshop/
├── labs/              # 軽量な活動記録（日付 + 場所 + 主催）
│   └── 20260425-shenzhen/
└── tasks/             # 再利用可能なステップ別チュートリアル、6 つのコア能力で分類
    ├── setup/         # ① 初期化、プロファイル、プロバイダ、チャネル
    ├── observe/       # ② コスト、トークン使用量、ゲートウェイ健全性
    ├── save/          # ③ メモリーワークスペース、LLM Wiki
    ├── apply/         # ④ OCR、写真 → フラッシュカード、ドキュメントパイプライン
    ├── build/         # ⑤ スキル、プラグイン、MCP、エージェント連携
    └── guard/         # ⑥ スキル監査、API キー保管庫、使用上限
```

**Lab（実験）** は 1 回の具体的な活動（日付 + 場所 + 主催）で、当日のアジェンダとして複数のタスクを選定します。
**Task（タスク）** は再利用可能なチュートリアルで、どの Lab からでも参照できます。

Lab は**開催地の言語**で書かれた単一の `README.md` のみを提供します（深圳場 = 中文、東京場 = 日本語）— 現場の聴衆に焦点を合わせた作りです。Task は `README.md`（英語）/ `README_CN.md`（中文）/ `README_JP.md`（日本語）の三言語 + `images/` キーフレームを揃えるため、どの地域の Lab からでもそのまま引用できます。

## 開催予定 Lab

| 日付 | 場所 | 主催 | タスクリスト |
|---|---|---|---|
| 2026-04-25 | [深圳](./labs/20260425-shenzhen/) 🇨🇳 中文 | OceanBase × 百度 | [Setup / wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/README_JP.md) → [Save / powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/README_JP.md) → [Build / ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/README_JP.md) |

## タスクインデックス

### ① Setup（初期化）
- [wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/README_JP.md) — セットアップウィザードから ERNIE + GLM を構成し、デフォルトモデルを決定
- [release-aware-upgrade](./tasks/setup/release-aware-upgrade/README_JP.md) — ClawMaster v0.4.0 のリリース通知機能：上部バナー → 設定ページ「ClawMaster Releases」セクションがリリースノートを描画、プラットフォームに応じてインストーラーを自動選択、GitHub → npm ミラーへフォールバック

### ② Observe（可観測性）
- [cron-cost-digest](./tasks/observe/cron-cost-digest/README_JP.md) — ClawProbe をインストールし、可観測ページの「日報サマリ」プリセットをワンクリックで cron 化、**今すぐ実行** で Observe × Cron ループを閉じる

### ③ Save（記憶）
- [powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/README_JP.md) — PowerMem に OpenClaw のファイル式記憶を引き継がせる：ブリッジ同期 → `MEMORY.md` のワンクリック取り込み → セマンティック召回の比較
- [cron-package-downloads-tracker](./tasks/save/cron-package-downloads-tracker/README_JP.md) — PowerMem を時系列ストアとして扱う：`package-download-tracker` cron が週次で npm / PyPI ダウンロード数を取得、観測を PowerMem に書き込み、翌週の実行がそれをベースラインとして読み込む

### ④ Apply（活用）
- [math-quiz-vision-webui](./tasks/apply/math-quiz-vision-webui/README_JP.md) — 概観ページから OpenClaw WebUI へ遷移、PaddleOCR を接続し、agent に手書き数学問題を OCR させて正答させる（算術 + 二次関数サンプル）

### ⑤ Build（構築）
- [ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/README_JP.md) — `content-draft` + `ernie-image` を有効化、LangChain DeepAgents ドキュメントをイラスト付き微信公衆号ドラフトに変換

### ⑥ Guard（安全）
- [skillguard-scan-compare](./tasks/guard/skillguard-scan-compare/README_JP.md) — スキルページで、クリーンなスキル（`linkedin-content` · A）とリスクあり（`baoyu-url-to-markdown` · C · 7 findings）を続けてスキャンし、SkillGuard が見ているものを確認

## 新タスクの投稿

1. `tasks/` 配下で能力域を 1 つ選ぶ（新規開設も可）
2. `tasks/<能力>/<タスク名>/` ディレクトリを作成し、`README.md` + `README_CN.md` + `README_JP.md` + `images/` を同梱
3. チュートリアルは以下を満たすこと：
   - **シナリオ優先** — このタスクが完了したら何が得られるかを冒頭で明示
   - **スクリーンショット駆動** — 主要な状態遷移ごとに 1 枚のキーフレーム
   - **そのまま通る** — コマンドはコピペで動くこと、「各自で補完」のような空白は残さない
4. PR を起票 — CI が Markdown 規約と三言語ファイルの有無を確認

## License

Apache-2.0、ClawMaster メインリポジトリと整合。
