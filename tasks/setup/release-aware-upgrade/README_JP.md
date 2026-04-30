# タスク：ClawMaster の新バージョンを検知してインストールする

**能力領域**：Setup（コア能力 #1）
**所要時間**：~5 分
**難易度**：入門（[wizard-ernie-glm](../wizard-ernie-glm/) の後）

> v0.4.0 から、ClawMaster は自分自身の新リリースを能動的に検出します。上部バナー → 設定ページの **ClawMaster Releases** セクションがリリースノートをレンダリング → **インストーラーを開く** ボタンが `navigator.platform` を見て OS 別アセットを自動選択。主ソースは GitHub Releases、フォールバックは npm メタデータ（設定済みの npm ミラーを尊重）。

> 🌐 本タスクは **中文優先** で執筆されました。完全版のウォークスルーは **[README_CN.md](./README_CN.md)** にあります。English stub：[README.md](./README.md)

## 主要フレーム

![](./images/01-banner.png)
*ダッシュボード上部のアップデートバナー。*

![](./images/02-release-section.png)
*設定ページの ClawMaster Releases セクション、アップデート利用可能な状態。*

![](./images/04-npm-fallback.png)
*GitHub が届かないときの npm フォールバック。*

![](./images/05-up-to-date.png)
*最新状態（対照）。*

## TL;DR

1. 上部バーに「**ClawMaster v{version} のアップデートが利用可能です**」のオレンジバナーが出る → **アップデートを表示** をクリック
2. **設定 → ClawMaster Releases** にジャンプ。GitHub ソースならセクションはリリース本文を Markdown としてレンダリング（見出し・箇条書き・コードブロック）
3. **インストーラードロップダウンはない。** `selectInstallerAsset(latestRelease, navigator.platform)` が自動で 1 つのアセットを選択（macOS → `.dmg`、Windows → `.msi`、Linux → `.AppImage`）。**インストーラーを開く** でそのアセットの GitHub URL を開く。横の **変更履歴** はリリースの `html_url` へ
4. 却下は **バージョン単位で localStorage 永続化**（キー接頭辞：`clawmaster-release-dismissed:`）。次の新リリースが出るまでバナーは再表示されない

## 主要な仕組み

- **現在バージョン**：Vite の `define` が `package.json#version` を `__CLAWMASTER_VERSION__` として注入、`src/lib/appVersion.ts` で `CLAWMASTER_VERSION` として読み出す
- **最新バージョン**：まず `GET https://api.github.com/repos/openmaster-ai/clawmaster/releases?per_page=10`（3 秒 AbortController 付き）、タイムアウト/失敗時はバックエンド `/api/npm/clawmaster-versions`（デスクトップは Tauri `list_clawmaster_npm_versions` コマンド）へフォールバック
- **npm ミラー**：フォールバック経路は Settings の npm プロキシ設定を尊重するので、中国本土など GitHub が届かない環境でも検知できる
- **Markdown**：レンダリング前にサニタイズされ、`<script>` や外部画像は除去。プレビューは 700 文字まで

完全版の手順・検証コマンド・FAQ（dev モードでバナーが残る／ノートが生 Markdown で出る／Tauri の shell `open` プラグイン allowlist／`navigator.platform` が空文字／ミラーが遅い 等）は [README_CN.md](./README_CN.md) を参照してください。
