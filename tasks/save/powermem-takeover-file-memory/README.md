# Task: Let PowerMem Take Over OpenClaw's File-Based Memory

**Capability**: Save (core capability #4)
**Time**: ~8 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Sync the OpenClaw bridge → one-click import `~/.openclaw/workspace/MEMORY.md` + `memory/*.md` → upgrade grep-style search to vector recall. After this, new `memory_add` calls land directly in managed PowerMem instead of rewriting workspace markdown.

> 🌐 This task was authored first in 中文. The complete walkthrough lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Memory**. The **PowerMem foundation** panel renders four cards on top.
2. In **OpenClaw bridge** subcard (state: *Needs sync*), click **Sync bridge**. ClawMaster installs the bundled `memory-clawmaster-powermem` plugin into OpenClaw and points the `memory` slot at it with the current profile's `dataRoot`.
3. In **Legacy memory import**, click **Import OpenClaw memories**. The bundled `workspaceImport` walks `~/.openclaw/workspace/MEMORY.md` + `memory/**/*.md`, fingerprints each by `(agentId, path, content)` SHA-256, and inserts one PowerMem record per file. Re-running is idempotent.
4. Scroll to **Why managed memory is better** to confirm coverage (imported / available sources) and to the **Recall comparison** row to pit PowerMem vector recall against the legacy `openclaw memory search` on the same query — queries that don't literally appear in the markdown still hit via vectors.

> ⚠️ Bridge showing **Unsupported** = web-only runtime. The managed runtime only activates in the desktop/Tauri build.

## Why this matters

OpenClaw's workspace memory is a folder of markdown that only grep can search. Once the bridge is synced, new captures go straight into PowerMem (SQLite or SeekDB), and semantic recall makes the assistant retrieve `user_role.md` from a paraphrase instead of needing a literal word match. The import step is a one-time idempotent migration with a persisted fingerprint state file — delete a markdown and its managed copy is removed on the next import.

See [README_CN.md](./README_CN.md) for the seeded-markdown bootstrap snippet, CLI verification against `openclaw.json` + `openclaw-import-state.json` + the PowerMem SQLite file, and troubleshooting for drift / failed imports / hidden legacy-compare column.
