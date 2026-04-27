# Task: Let PowerMem Take Over OpenClaw's File-Based Memory

**Capability**: Save (core capability #4)
**Time**: ~10 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Sync the OpenClaw bridge → one-click import `~/.openclaw/workspace/MEMORY.md + memory/*.md` → write one new fact through the UI → run a semantic search → let an `openclaw agent` turn recall the whole bundle in a fresh session. The walkthrough shows, end to end, why PowerMem beats file-based memory.

> 🌐 This task was authored first in 中文. The complete walkthrough — seed snippet, screenshots, troubleshooting — lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Memory**. The PowerMem foundation panel shows four summary cards on top plus an **OpenClaw bridge** subcard. Click **Sync bridge** — ClawMaster installs the bundled `memory-clawmaster-powermem` plugin into OpenClaw, points `plugins.slots.memory` at it, and wires `plugins.entries.memory-clawmaster-powermem` to the current profile's `dataRoot`.
   ![Overview with bridge ready](./images/01-overview.png)
2. **Legacy memory import → Import OpenClaw memories**. `workspaceImport` walks `~/.openclaw/workspace/MEMORY.md + memory/**/*.md`, fingerprints each via `(agentId, path, content)` SHA-256, and inserts one PowerMem record per file. Idempotent: re-run shows everything in `skipped`; edited files land in `updated`; deleted files cascade-remove the managed record.
   ![Import-after stats](./images/02-import-after.png)
3. Scroll to **Why managed memory is better** — the three proof cards confirm coverage, direct-write count, and recall path.
   ![Proof cards — 4/4 imported, 1 direct, legacy CLI unavailable](./images/03-proof-cards.png)
4. Write one new fact into the big input (e.g. *"The Shenzhen venue is T Space B2 801, open 30 min early for projector + extension cords"*), then search **"下一场工作坊在哪里" / "where is the next workshop"**. Imported and UI-written memories rank in the same result list with scores.
   ![Semantic search ranks both workspace-imported and UI-written memories](./images/04-search-hit.png)
5. **Recent managed memories** tags each record by origin so you can tell the path at a glance.
   ![Recent list: "Written from memory page" vs "Imported workspace"](./images/05-recent-mixed.png)
6. Start a fresh `openclaw agent` turn — it pulls the facts back through `memory_recall`:
   ```bash
   $ openclaw agent -m "Where's my next workshop?" --agent main --local
   [plugins] memory-clawmaster-powermem: auto-captured 1 memory chunk(s)

   Based on your long-term memory, the next in-person workshop is in
   **Shenzhen, T Space B2 room 801** on **2026-04-25**.
   Arrive 30 min early to set up the projector and extension cords.
   ```
   Note how the answer fuses the workspace-imported fact (Shenzhen / 2026-04-25) with the UI-written fact (T Space / 30 min / projector). The **Sessions** page records the turn with its token draw:
   ![Sessions page: active agent session consuming memory context](./images/06-session-active.png)

> ⚠️ Bridge showing **Unsupported** = web-only runtime. The managed runtime only activates in the desktop/Tauri build.

## Why this matters

OpenClaw's workspace memory is a folder of markdown that only grep can search. Once the bridge is synced and the import runs, new captures go straight into PowerMem (SQLite today, SeekDB tomorrow), and semantic recall lets the agent hit `user_role.md` from a paraphrase — no literal word match needed. The import is a one-shot idempotent migration with a persisted fingerprint state file (`openclaw-import-state.json`) — delete a markdown and its managed copy is cleaned up on the next import.

New ≥2026.4.15 OpenClaw builds have removed the `openclaw memory search` subcommand, so the legacy-vs-managed side-by-side panel is "unavailable" by design. That's the point: the grep-style path is no longer maintained, and the managed semantic search is the only way forward.

See [README_CN.md](./README_CN.md) for the seeded-markdown bootstrap, CLI verification against `openclaw.json` + `openclaw-import-state.json` + the PowerMem SQLite file, a second cross-session recall example (writing-style preferences), and troubleshooting for drift / failed imports / blocked plugin install.
