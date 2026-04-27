# Task: Install ClawProbe and Wire a Daily Cost Digest via the Observe Template

**Capability**: Observe (core capability #2) + Cron
**Time**: ~8 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Install ClawProbe → seed one agent turn so the **Latest session** card lights up → click the built-in **Daily digest** preset on the Observe page → save → hit **Run now** for instant feedback → head back to Observe and watch the card follow the cron session → drop into the WebUI to see the tool-call timeline. An end-to-end Observe × Cron loop.

> 🌐 This task was authored first in 中文. The complete walkthrough — all 8 key frames, verification commands, FAQs — lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Observe**. Empty state shows **Install & Start ClawProbe**. Click it (or run `npm i -g clawprobe && clawprobe start`).
2. Seed a turn from another terminal so the Latest-session card has data: `openclaw agent -m "should I bring an umbrella today?" --agent main --local`. Refresh Observe — the card now shows model, session cost, in/out tokens, last active, context utilisation, and session key.
3. Scroll to **Scheduled cost digests** → click **Daily digest → Open in Cron**. The form lands at `/cron?template=cost-digest&period=day` fully prefilled (cron `0 8 * * *`, Asia/Shanghai, isolated session, `clawprobe-cost-digest` skill prompt).
4. Only two edits needed:
   - **Model**: paste `siliconflow/deepseek-ai/DeepSeek-V3` (keep the `provider/` prefix — without it the cron errors with `FailoverError: Unknown model`)
   - Uncheck **Announce summary to channel** unless you've already wired a chat channel (otherwise the first run fails with `Channel is required`)
5. Save. The cron appears with next-run 08:00 tomorrow. Don't wait: click **Run now**. It turns 🟢 **ok** in ~60–90s.
6. **Run history** drawer shows the markdown digest (total cost, top models, top sessions). **Open in WebUI** deep-links to the cron's OpenClaw chat session showing the real `read` + `exec` tool calls that produced it.
7. Back on Observe: today's cost bumps up, and the **Latest session** card now points to `agent:main:cron:<jobId>:run:<runId>` — the cron's isolated session — closing the Observe × Cron feedback loop.

> ⚠️ Why daily (not the old "1 min from now" design)? Single-shot in 80 seconds is too short to switch tabs and compare, and once it fires the job self-destructs — nothing left to discover. A daily cron + Run Now gives you **a realistic schedule you'll actually use** plus **instant feedback** during the workshop.

## Why this matters

ClawMaster splits **passive visualisation** (Observe reads ClawProbe's SQLite) from **active automation** (Cron runs skills on a schedule). The Observe page ships three digest presets (day/week/month) that generate a prefilled cron draft in a single click, so wiring probe → cron → digest → back-into-observe takes one minute of clicking, not manual cron-expression plumbing. The same pattern works for anomaly alerts, scheduled cleanups, or any recurring skill invocation.

The new **Latest session** card (shipped in b11da4f) is the first place you check when "costs spiked out of nowhere" or "context is almost full" — it exposes session cost, in/out token counts, context utilization, and the session key at a glance, pulling from `clawprobe session --json` via the ClawMaster backend.

See [README_CN.md](./README_CN.md) for the full 6-step walkthrough, `jq` / `curl` cross-verification, unpriced-model fix, and FAQ for `FailoverError`, `Channel is required`, grey **Open in WebUI** button, and why this task moved off the 1-minute one-shot pattern.
