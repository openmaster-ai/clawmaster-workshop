# タスク：PowerMem に OpenClaw のファイルベース記憶を引き継がせる

**能力**: Save（コア能力 #4）
**所要時間**: 約 8 分
**難易度**: 初級（[wizard-ernie-glm](../../setup/wizard-ernie-glm/) 完了後）

> OpenClaw ブリッジを同期 → `~/.openclaw/workspace/MEMORY.md` + `memory/*.md` をワンクリックでインポート → grep 検索をベクトル召喚にアップグレード。以降、新しい `memory_add` はワークスペースの markdown を書き換えず、直接 PowerMem に入ります。

> 🌐 このタスクは中文で初筆。詳細は **[README_CN.md](./README_CN.md)** を参照。English: [README.md](./README.md)

## TL;DR

1. 左ナビ → **記憶**。**PowerMem 基盤** パネルに 4 枚のサマリーカード。
2. **OpenClaw ブリッジ** サブカード（状態: *要同期*）で **ブリッジ同期** をクリック。ClawMaster が同梱の `memory-clawmaster-powermem` プラグインを OpenClaw に配置し、`memory` スロットを現在 profile の `dataRoot` に向けます。
3. **旧メモリ取り込み** で **OpenClaw メモリを取り込む** をクリック。`workspaceImport` が `~/.openclaw/workspace/MEMORY.md` + `memory/**/*.md` を走査し、`(agentId, path, content)` の SHA-256 指紋でファイルごとに 1 件の PowerMem レコードを挿入します。再実行は冪等。
4. **なぜマネージド記憶が優れているか** のカバレッジ 3 枚と **召喚比較** でベクトル召喚 vs 旧ファイル grep を同じクエリで並列実行。markdown に逐語マッチがないクエリでもベクトルで命中します。

> ⚠️ ブリッジが **非対応** → web 専用ランタイムです。desktop/Tauri ビルドでのみマネージドランタイムが有効化されます。

詳細な手順・CLI 検証・トラブルシュートは [README_CN.md](./README_CN.md) を参照。
