# Task: Use PowerMem as the Time-Series Store for `package-download-tracker`, Wire a Weekly Package Download Cron

**Capability**: Save + Cron
**Time**: ~12 min
**Level**: Intermediate (after [cron-cost-digest](../../observe/cron-cost-digest/) + [powermem-takeover-file-memory](../powermem-takeover-file-memory/))

> Find the bundled `package-download-tracker` skill in the catalog → paste a URL to load a prefilled cron form → **Run now** once so the skill hits npm/PyPI and writes observations to PowerMem → drop into WebUI to inspect the real tool-call flow → check `/memory` and see three new entries. Promotes PowerMem from "fact index" to "time-series backend for recurring skills" — the *Previous week* column only truly fills in a week from now, but today you watch the seed get planted and verify the recall plumbing is wired end-to-end.

> 🌐 This task was authored first in 中文. Full walkthrough — 6 key frames, verification block, FAQ — lives at **[README_CN.md](./README_CN.md)**. 日本語：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Skills** → scroll to *Currently installed in workspace*, find **`package-download-tracker`** (the card description names npm + PyPI, weekly/monthly, stored history observations). There is **no cron-template picker card** on `/cron` for this skill yet — the entry point is a URL.
2. Paste `http://localhost:16223/cron?template=package-downloads&period=week` into the address bar. A toast loads the template and the **New cron job** modal opens prefilled:
   - Name / description / message (full skill prompt with sample packages `clawmaster, powermem` on npm and `powermem` on PyPI)
   - Cron `0 8 * * *`, `Asia/Shanghai`, isolated session, agent `main`
   - **Only two manual edits:**
     - **Model**: paste `siliconflow/deepseek-ai/DeepSeek-V3` — the `provider/` prefix is required or cron errors with `FailoverError: Unknown model`
     - **Message**: optionally swap sample package names, but keep the `npm packages \`...\`; PyPI packages \`...\`` shape and all the "prefer stored history / do not mention memory mechanics / do not invent numbers" constraints intact
3. Save → **Run now**. ~30 s later the job turns 🟢 **ok**. The run-history drawer shows only timing; to see the actual markdown you must click **Open in WebUI**.
4. In the WebUI chat view: the final Tool-role message has a table with columns *Current week / Previous week / Change*. Expect:
   - npm packages: `Previous week = n/a`, change column = "no previous-period data" (cold start, PowerMem has no history)
   - PyPI packages: `Previous week` has a real number and a real percentage — **not from PowerMem**, the `pypistats /recent` endpoint returns the previous window itself
5. Open the **Memory** page (or `openclaw ltm list --json | grep 'package-download-tracker registry:'`). Three new `reference`-style entries now exist, each is plain text: `package-download-tracker registry:<r> package:<p> period:week` + `summary: ...` + `snapshot-json: {...}`. The `snapshot-json:` marker is what the next run's `--load-memory` grep uses.
6. **Run now** a second time **on the same day**: the table looks identical — npm is still `n/a`, PyPI still shows the same delta. This is by design: same-week observations stored in PowerMem don't count as "previous-week" because the *current* window overlaps them. The true *PowerMem-as-time-series* payoff only shows once `fetchedAt` falls in a prior calendar week — i.e., tomorrow's or next week's scheduled 08:00 run. Today you've planted the seed.
7. Inspect the tool-call timeline in the WebUI:
   - Good path: `session_status → sessions_spawn → sessions_yield` — the agent spawns a subagent that actually runs `read SKILL.md` + `exec track-downloads.mjs --load-memory --save-memory`
   - Bad path: `Tool package-download-tracker not found` — the agent tried to call the skill as a built-in tool (it's not, it's `read` + `exec`). When this happens the skill never re-executes; the agent composes a reply from the `<relevant-memories>` context, and PowerMem gets zero new observations. Quick diagnostic: count `package-download-tracker registry:` entries before and after the run; if the count didn't grow, the skill didn't actually run

> ⚠️ Why in `save/`, not `observe/`: `clawprobe-cost-digest` regenerates markdown from SQLite every run and is memory-free. `package-download-tracker` uses PowerMem as persistent time-series storage — that's the actual novelty vs. the existing cost-digest cron, so this task lives in Save.

See [README_CN.md](./README_CN.md) for the 6-step walkthrough with screenshots, `jq` / CLI cross-verification, and the FAQ covering cold-start zeros, why *Previous week* stays `n/a` on same-day re-runs, gateway auto-capture pollution (filtered out by the skill's `snapshot-json:` marker but visible in the PowerMem UI), how to diagnose "tool not found" regressions, why `--period day` isn't supported, and the PowerMem cleanup incantation for accumulated observations.
