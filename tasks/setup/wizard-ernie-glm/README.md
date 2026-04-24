# Task: Configure ERNIE + GLM via the Setup Wizard

**Capability**: Setup (core capability #1)
**Time**: ~10 minutes
**Level**: Beginner

> Use the two-step setup wizard to detect OpenClaw, validate API keys for two Chinese LLM providers (Baidu ERNIE + Zhipu GLM), pick a default model, and land on the ClawMaster console.

> 🌐 **This task was authored first in 中文** — the most detailed and screenshot-accurate version is **[README_CN.md](./README_CN.md)**. An English version will follow. 日本語版：[README_JP.md](./README_JP.md)

## Prerequisites

1. **Node.js 22+ LTS** with **npm 10+** — `node -v` must print v22 or higher. Node 20 enters maintenance this month; fresh installs should go straight to 22
2. Two tokens:
   - Baidu AI Studio — <https://aistudio.baidu.com/usercenter/token>
   - Zhipu BigModel — <https://open.bigmodel.cn/> API Keys page
3. No existing OpenClaw config on this machine (or `~/.openclaw/openclaw.json` cleared)

## TL;DR

1. Install: `npm i -g clawmaster@rc && clawmaster`
2. Open <http://localhost:16223> — the wizard boots
3. Wait for engine detection to finish
4. Keep the default **文心大模型 / ERNIE** selected, paste your Baidu AI Studio token, click **Verify & Continue**, pick a model
5. Click **Custom (OpenAI Compatible)**, enter:
   - Base URL: `https://open.bigmodel.cn/api/paas/v4`
   - API Key: your Zhipu key
6. Verify, type model ID (e.g. `glm-5.1`), click **Enter ClawMaster**
7. You land on the dashboard with both providers configured

## Key frames

![](./images/01-engine-detecting.png)
*Engine detection — the wizard checks for a local OpenClaw install.*

![](./images/02-provider-tiers.png)
*Tiered provider picker. ERNIE sits in the golden-sponsor tier; GLM is reached via Custom (OpenAI Compatible) in the bottom tier.*

![](./images/04-ernie-models.png)
*After a successful key probe, the wizard dynamically loads the provider's model catalog.*

![](./images/10-complete.png)
*Final step: GLM filled, model ID entered, ready to finish.*

See [README_CN.md](./README_CN.md) for the full walkthrough with all 10 key frames, troubleshooting, and verification commands.
