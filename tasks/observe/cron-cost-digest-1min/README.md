# Task: Enable Observe + Fire a One-Shot Cron 1 Minute From Now for ClawProbe Cost

**Capability**: Observe (core capability #2) + Setup (cron sub-module)
**Time**: ~8 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Enable the Observe module, then create a **single-shot** cron job via the GUI that fires 1 minute from now, runs the `clawprobe-cost-digest` skill, and drops today's model cost digest into the run history.

> 🌐 This task was authored first in 中文. The complete walkthrough with screenshots lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Observe**. If empty, click "Install & Start ClawProbe" (or run `npm i -g clawprobe && clawprobe start`)
2. Left nav → **Cron** → `+ New job`
3. Name: `1-minute cost snapshot` · Schedule type: **Run once** · Run-at: ISO timestamp ~80s from now (`python3 -c "import datetime as d; t=d.datetime.now().astimezone()+d.timedelta(seconds=80); print(t.isoformat(timespec='seconds'))"`)
4. **Set `model` explicitly** to your current default (cron pins its own model; leaving it blank freezes whatever was the default at first-run time)
5. Message: paste the `clawprobe-cost-digest` skill prompt (see Chinese README for the exact string)
6. Save. Wait ~1 min. After it fires, click **"Open in WebUI"** on the cron card — it deep-links to the agent's cron session with the auth token prefilled, and you see tool calls + final digest inline. The "Run history" drawer shows the same stdout summary in a smaller footprint; `curl /api/clawprobe/cost?period=day` cross-checks the numbers.

> ⚠️ `clawprobe status` reports the **last-active session's** model, not your default. Cron sessions keep a snapshotted model; if your status shows a model you're not currently using interactively, that's a cron session still on an older pin — see the Chinese FAQ for the fix.

## Why this matters

ClawMaster splits passive visualisation (Observe reads ClawProbe's SQLite) from active automation (Cron runs skills on a schedule). Wiring the two together — **cron → skill → ClawProbe → back into the run log** — is the canonical pattern for recurring reports, anomaly alerts, or scheduled cleanups.

See [README_CN.md](./README_CN.md) for the full walkthrough: 5 key frames, CLI verification (`curl /api/clawprobe/cost?period=day`), follow-up tasks, troubleshooting for empty run output, gateway dependencies, and 6-field cron expression tips.
