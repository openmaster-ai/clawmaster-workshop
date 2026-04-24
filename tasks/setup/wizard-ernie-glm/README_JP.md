# タスク：セットアップ ウィザードで ERNIE + GLM を設定

**能力域**：Setup（コア能力 #1）
**所要時間**：約 10 分
**レベル**：初級

> 2 ステップのセットアップ ウィザードを使い、OpenClaw 検出・中国系 LLM プロバイダ 2 社（Baidu ERNIE + Zhipu GLM）の API キー検証・デフォルト モデル選択を行い、ClawMaster コンソールに到達します。

> 🌐 **このタスクは最初に中国語で執筆** されました — スクリーンショットと最も整合する完全版は **[README_CN.md](./README_CN.md)** です。日本語訳は追って提供予定。English: [README.md](./README.md)

## 前提条件

1. **Node.js 22+ LTS** と **npm 10+**（`node -v` が v22 以上。Node 20 は今月 maintenance 入り、新規インストールは直接 22 を推奨）
2. 2 種類のトークン：
   - Baidu AI Studio — <https://aistudio.baidu.com/usercenter/token>
   - Zhipu BigModel — <https://open.bigmodel.cn/> の API Keys ページ
3. 本機に既存の OpenClaw 設定がない（あるいは `~/.openclaw/openclaw.json` を削除済み）

## 手順サマリ

1. インストール：`npm i -g clawmaster@rc && clawmaster`
2. <http://localhost:16223> を開く — ウィザードが起動
3. エンジン検出が完了するまで待つ
4. デフォルトの **文心大模型 / ERNIE** のまま、Baidu AI Studio トークンを貼り付け、**「验证并继续」** をクリック、モデルを選択
5. **Custom (OpenAI Compatible)** をクリックし、以下を入力：
   - Base URL：`https://open.bigmodel.cn/api/paas/v4`
   - API Key：Zhipu のキー
6. 検証 → モデル ID（例：`glm-5.1`）を入力 → **「进入管理大师」**
7. ダッシュボードに遷移し、両プロバイダが登録済み

## キーフレーム

![](./images/01-engine-detecting.png)
*エンジン検出 — ローカル OpenClaw インストールを確認。*

![](./images/02-provider-tiers.png)
*階層化された Provider ピッカー。ERNIE はゴールド スポンサー層、GLM は最下層の Custom (OpenAI Compatible) から接続。*

![](./images/04-ernie-models.png)
*キー検証成功後、Provider のモデル カタログが動的にロードされる。*

![](./images/10-complete.png)
*最終ステップ：GLM 設定済み、モデル ID 入力済み、完了直前。*

全 10 フレーム・トラブルシュート・検証コマンドを含む完全版は [README_CN.md](./README_CN.md) を参照してください。
