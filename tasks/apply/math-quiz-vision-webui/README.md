# Task: OCR a Handwritten Math Quiz in OpenClaw WebUI and Have the Agent Solve It

**Capability**: Apply (core capability #4)
**Time**: ~12 minutes
**Level**: Beginner+ (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Open the OpenClaw native chat UI from the ClawMaster overview shortcut, configure the bundled **PaddleOCR document-parsing** capability, generate a handwritten-style math quiz with `ernie-image`, drop it in the workspace, and ask the agent. The agent auto-reads the skill doc, execs the parse script, reads back the PaddleOCR markdown, and returns the right answers — all within the WebUI.

> 🌐 This task was authored first in 中文. The complete walkthrough — 9 key frames, CLI verification, FAQ on the `read`-is-not-vision gotcha, and the AI Studio deploy cost sketch — lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. ClawMaster → **Overview** → top-right **Open OpenClaw WebUI**. New tab lands at `http://127.0.0.1:18789/?token=...` — the OpenClaw native chat UI.
2. In ClawMaster → **OCR**, paste your deployed Baidu AI Studio PaddleOCR endpoint (`https://…aistudio-app.com/layout-parsing`) + AI Studio token, click **Save and enable OCR**. Under the hood: `ocr.providers.paddleocr` is written, the bundled `paddleocr-doc-parsing` skill is linked into `~/.openclaw/workspace/skills/`, and `skills.entries.paddleocr-doc-parsing.enabled = true`.
3. Generate a handwritten math quiz with `ernie-image` — any style that looks real (arithmetic for kids, quadratic for middle school) — and save it to `~/.openclaw/workspace/images/math-quiz.png`.
4. Back in the WebUI, start a fresh session, keep the default text model (DeepSeek-V3 works — no VL needed once OCR is in the loop), and prompt: *"Image is at /path/to/math-quiz.png. First `read` ~/.openclaw/workspace/skills/paddleocr-doc-parsing/SKILL.md, then `exec` `parse-document.mjs` to get markdown, then answer every problem."*
5. Agent runs the loop in ~60–90s: `read → read → exec → final answer`. For the arithmetic image you get `70 / 37 / 84 / 18`; for a quadratic `y = x² − 4x + 3` quiz you get `vertex (2, -1)` and `y=8 at x=5`, with LaTeX-formatted working shown step by step.

> ⚠️ The `read` tool in OpenClaw **is not a vision tool** — it returns text. The whole point of wiring PaddleOCR is that it converts the image to structured markdown that a text LLM can then reason about. If you skip the OCR step and just attach the image to the WebUI composer, the agent calls `read` on a fabricated file path, fails ENOENT, and gives up. Be explicit: name the skill, name the script, name the image path.

## Why this matters

The Apply capability is where all four other capabilities pay off: Setup gives you the model + OCR credentials, Observe lets you see the token burn, Save keeps the user's "favourite worked example" across sessions, Guard checks that the OCR skill you just linked isn't hiding `child_process` landmines. This task is the most direct demo of *agent as middleware*: the user hands over a picture, the agent decides which skill to use, invokes the skill's bundled scripts via `exec`, integrates the structured output, and returns a human-readable answer — no external OCR SaaS UI, no second browser tab, no copy-paste.

The reason we chose PaddleOCR over a direct call to a vision LLM (Qwen3-VL, GLM-4V) is pragmatic: **OpenClaw WebUI's composer image attachment currently routes through the agent's `read` tool, which is text-only — so a "just attach + ask" flow silently fails.** Wiring OCR explicitly gives a deterministic, auditable path where the agent's tool calls are visible in the chat (read SKILL.md → exec script → quote markdown → solve) and the cost is predictable. See [README_CN.md](./README_CN.md) for the full 6-step walkthrough, the AI Studio endpoint setup, CLI cross-checks, and FAQ covering `Unrecognized key: "ocr"` from older openclaw CLIs, the environment-variable fallback, and the follow-on idea of bridging this to a Feishu/iMessage "photo in → answer out" channel.
