# Task: Turn a URL Into an Illustrated WeChat Article With ernie-image + content-draft

**Capability**: Build (core capability #5) + Skills sub-module
**Time**: ~15 minutes
**Level**: Beginner → Intermediate (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Confirm the bundled `ernie-image` skill is live, one-click install the bundled `content-draft` workflow, then from the ClawMaster dashboard open the OpenClaw console, drop in a LangChain docs URL, and watch the agent extract → illustrate → save a WeChat draft. View the illustrated Markdown on ClawMaster's **Content Drafts** page.

> 🌐 This task was authored first in 中文. The complete walkthrough lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Skills**. Verify `ERNIE-Image Guide` shows **Enabled** (it auto-installs when you add the Baidu ERNIE provider during the setup wizard).
2. On the same page, click **Install** on the `Content Draft` card — it's bundled, so it copies from `clawmaster/bundled-skills/content-draft/` in 1–2 seconds, no network.
3. Dashboard → **Open OpenClaw WebUI** → **Sessions** tab. Open or create a session.
4. Paste:
   ```
   Based on https://docs.langchain.com/oss/python/deepagents/overview
   generate an illustrated WeChat article targeting Chinese developers
   who are evaluating agent frameworks.
   ```
5. `content-draft` runs its 7-step default workflow (confirm platform → `fetch-url-markdown.mjs` extract → `memory_recall` → write draft → `ernie-image` illustration → `save-draft-artifacts.mjs` → `build-chat-response.mjs`). Artifacts land in `~/.openclaw/workspace/content-drafts/<run-id>/wechat/`.
6. Back in ClawMaster, left nav → **Content Drafts** to view the rendered Markdown + embedded hero image.

> ⚠️ The primary chat model should be a thinking-capable one (`ernie-5.0-thinking-preview` or `glm-5.1`), not `ernie-image-turbo`. `ernie-image` is invoked as a **tool**, it should not be the top-level agent model.

## Why this matters

`content-draft` is OpenClaw's repo-owned URL-to-draft workflow and explicitly prefers `ernie-image` for illustrations when available. Together they demonstrate the Build capability end-to-end: installing skills, giving them a target artifact (WeChat post), and letting the agent orchestrate extraction, drafting, illustration, and artifact saving in one turn. ClawMaster's **Content Drafts** page then gives you a first-class UI for browsing the saved run directories.

See [README_CN.md](./README_CN.md) for the full walkthrough with bundled-script arguments, verification via CLI + ClawProbe cost trace, troubleshooting for rate limits and empty draft pages, and follow-up tasks (schedule a cost digest, write a style memory, clawvet third-party skills).
