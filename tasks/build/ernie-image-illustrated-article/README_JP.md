# タスク：ernie-image + content-draft で 1 本の URL をイラスト付き WeChat 記事に

**能力**：Build（コア能力 #5）+ Skills サブモジュール
**所要時間**：約 15 分
**レベル**：初級 → 中級（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> バンドルされた `ernie-image` スキルが有効なことを確認し、バンドルされた `content-draft` ワークフローをワンクリックでインストール。ClawMaster のダッシュボードから OpenClaw コンソールを開いて LangChain ドキュメントの URL を投入すると、エージェントが本文抽出 → イラスト生成 → WeChat 下書き保存を一気通貫で実行する。最後に ClawMaster の **コンテンツ草稿** ページで完成物を確認する。

> 🌐 本タスクの主版は中国語です。完全な手順は **[README_CN.md](./README_CN.md)** を参照してください。English：[README.md](./README.md)

## TL;DR

1. 左ナビ → **Skills**。`ERNIE-Image Guide` が **有効** 状態であることを確認（文心プロバイダー追加時に自動インストールされます）。
2. 同じページで `Content Draft` カードの **インストール** をクリック。bundled なのでネットワーク不要、1〜2 秒で完了。
3. ダッシュボード → **OpenClaw WebUI を開く** → **Sessions** タブ。セッションを開く/新規作成。
4. 次のメッセージを送信：
   ```
   https://docs.langchain.com/oss/python/deepagents/overview を元に、
   Agent フレームワークを評価している中国語圏の技術者向けに、
   イラスト付きの WeChat 公式アカウント記事を生成してください。
   ```
5. `content-draft` が 7 ステップのデフォルトワークフロー（platform 確認 → `fetch-url-markdown.mjs` 抽出 → `memory_recall` → 本文執筆 → `ernie-image` で挿絵 → `save-draft-artifacts.mjs` → `build-chat-response.mjs`）を実行。成果物は `~/.openclaw/workspace/content-drafts/<run-id>/wechat/` に保存される。
6. ClawMaster に戻って左ナビ → **コンテンツ草稿** を開き、レンダリングされた Markdown とヒーロー画像を確認。

> ⚠️ 主要な対話モデルは思考能力のあるもの（`ernie-5.0-thinking-preview` または `glm-5.1`）を選択してください。`ernie-image-turbo` は **ツールとして呼び出される画像生成モデル** であり、エージェントのトップレベルモデルにしてはいけません。

## なぜ重要か

`content-draft` は OpenClaw リポジトリ公式の「URL から下書き」ワークフローで、イラスト生成時は `ernie-image` を最優先で使います。この 2 つを組み合わせることで、Build 能力のエンドツーエンド体験（スキル導入 → 目的成果物の指示 → 抽出・執筆・イラスト・保存の自動オーケストレーション）が実現します。ClawMaster の **コンテンツ草稿** ページは `~/.openclaw/workspace/content-drafts/<run-id>/` 下のディレクトリを一覧表示する一級 UI です。

完全な手順（スクリプト引数、CLI + ClawProbe コストトレースによる検証、レート制限や空の草稿ページのトラブルシューティング、次のタスクへの導線）は [README_CN.md](./README_CN.md) を参照してください。
