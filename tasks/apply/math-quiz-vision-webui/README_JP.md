# タスク：OpenClaw WebUI で手書きの数学問題を PaddleOCR にかけ、agent に解かせる

**能力域**：Apply · **所要**：約 12 分 · **レベル**：初級+（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> ClawMaster 概要ページ右上の **OpenClaw WebUI を開く** から OpenClaw 純正チャット UI にジャンプ。**PaddleOCR 文書解析** を配線し、`ernie-image` スキルで手書き風の算数問題を生成してワークスペースへ保存、agent に投げる。agent が自動でスキルドキュメントを `read`、`exec` で parse スクリプト起動、PaddleOCR の markdown を読み戻して正答を返す。全部 WebUI 内で完結。

> 🌐 このタスクは中文で初筆。9 枚のキーフレーム・CLI 検証・`read` が vision でない点の FAQ・AI Studio デプロイコスト概算を含む完全版は **[README_CN.md](./README_CN.md)**。English：[README.md](./README.md)

## TL;DR

1. ClawMaster → **概览** → 右上 **OpenClaw WebUI を開く**。新しいタブで `http://127.0.0.1:18789/?token=...` に着地 — これが OpenClaw 純正チャット
2. ClawMaster → **OCR** ページで、Baidu AI Studio にデプロイ済みの PaddleOCR エンドポイント（`https://…aistudio-app.com/layout-parsing`）と AI Studio トークンを貼り付け、**保存并启用 OCR** をクリック。裏で `ocr.providers.paddleocr` が書き込まれ、同梱の `paddleocr-doc-parsing` スキルが `~/.openclaw/workspace/skills/` に link され、`skills.entries.paddleocr-doc-parsing.enabled = true` になる
3. `ernie-image` で手書き風の数学問題（算数 or 二次関数など）を生成し `~/.openclaw/workspace/images/math-quiz-1.png` に保存
4. WebUI に戻り新しいセッションを開始。モデルはデフォルト（DeepSeek-V3 / qianfan-code-latest など純テキストで OK、OCR を挟むので VL 不要）のまま、人間らしく一言：**`OCR 一下 workspace/images 里的数学题并给出正确答案。`** スキル名・スクリプトパス・ファイル名は書かない
5. agent は 60〜90 秒で自律実行：`exec ls` でファイル検出 → `exec parse-document.mjs`（OCR スキルのスクリプトを skill の `description` から発見）→ 正解を返す。算数は `70 / 37 / 84 / 18`、二次関数 `y = x² − 4x + 3` なら `頂点 (2, -1)` と `x=5 のとき y=8` を LaTeX 付きで

> ⚠️ シンプルなプロンプトにするのは **スキルが実際に動く事を証明する** ため。「まず SKILL.md を read、次に parse-document.mjs を exec」と手取り足取り書いたら、それは人間がオーケストレーションしているだけで、スキルが自律的にマッチしている証明にならない。スキルを有効化 → 普通の中国語で投げる → agent がマッチするツールを自ら選ぶ、が本当のテスト。

## なぜこの設計か

Apply 能力は他 4 つが活きる場所：Setup でモデル + OCR クレデンシャル、Observe でトークン消費が見える、Save で「ユーザーのお気に入り例題」がセッション間で残り、Guard で link したスキルに `child_process` 地雷が無いか確認 — このタスクは *agent が middleware* である事を最も直接的に示す。画像を渡す → agent がスキル選定 → skill 同梱スクリプトを `exec` → 構造化結果を統合 → 人間可読な答案を返す。外部 OCR SaaS の画面不要、2 つ目のブラウザタブ不要、コピペ不要。

VL モデルに直接投げる（Qwen3-VL / GLM-4V）のでなく PaddleOCR を選んだ理由は実用性：**OpenClaw WebUI の画像添付は現状 agent の `read` ツール経由でテキスト扱いなので、「貼って聞く」フローは静かに失敗する。** OCR を明示的に配線すると、agent のツールコールがチャット上に可視化され（SKILL.md を read → script を exec → markdown を引用 → 解答）、コストも予測可能。詳しい手順・スクリーンショット・CLI 確認・旧 openclaw CLI で `Unrecognized key: "ocr"` が出る場合の対処・環境変数フォールバック・Feishu/iMessage「写真投げ→答案戻し」bot へ発展させるヒントは [README_CN.md](./README_CN.md) を参照。
