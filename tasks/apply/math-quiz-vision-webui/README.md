# Task: OCR a Handwritten Math Quiz in OpenClaw WebUI and Have the Agent Solve It

**Capability**: Apply (core capability #4)
**Time**: ~12 minutes
**Level**: Beginner+ (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Open the OpenClaw native chat UI from the ClawMaster overview shortcut, configure the bundled **PaddleOCR document-parsing** capability, generate a handwritten-style math quiz with `ernie-image`, drop it in the workspace, and ask the agent. The agent auto-reads the skill doc, execs the parse script, reads back the PaddleOCR markdown, and returns the right answers — all within the WebUI.

> 🌐 This task was authored first in 中文. The complete walkthrough — 9 key frames, CLI verification, FAQ on the `read`-is-not-vision gotcha, and the AI Studio deploy cost sketch — lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. ClawMaster → **Overview** → top-right **Open OpenClaw WebUI**. New tab lands at `http://127.0.0.1:18789/?token=...` — the OpenClaw native chat UI.
2. In ClawMaster → **OCR**, paste your deployed Baidu AI Studio PaddleOCR endpoint (`https://…aistudio-app.com/layout-parsing`) + AI Studio token, click **Save and enable OCR**. Under the hood: `ocr.providers.paddleocr` is written, the bundled `paddleocr-doc-parsing` skill is linked into `~/.openclaw/workspace/skills/`, and `skills.entries.paddleocr-doc-parsing.enabled = true`.
3. Generate a handwritten math quiz with `ernie-image` (arithmetic for kids, quadratic for middle school) and save it to `~/.openclaw/workspace/images/math-quiz-1.png`.
4. Back in the WebUI, start a fresh session, keep the default text model (DeepSeek-V3 / qianfan-code-latest both work — no VL needed once OCR is in the loop), and ask like a human: **`OCR 一下 workspace/images 里的数学题并给出正确答案。`** No skill name, no script path, no filename spelled out.
5. Agent runs autonomously in ~60–90s: `exec ls` to find the image → `exec parse-document.mjs` (the OCR skill script, which the agent discovered via the enabled skill's `description`) → returns correct answers. Arithmetic → `70 / 37 / 84 / 18`. Quadratic `y = x² − 4x + 3` → `vertex (2, -1)` and `y = 8 at x = 5`, with LaTeX step-by-step working.

> ⚠️ The whole point of the simple prompt is to **prove the skill works**. If you spoon-feed `"first read SKILL.md, then exec parse-document.mjs"`, you're just orchestrating yourself — the skill hasn't really earned its place. The test is: enable the skill, say what you want in plain Chinese, and see if the agent picks up the matching tool on its own.

## Why this matters

The Apply capability is where the other four pay off: Setup gives you the model + OCR credentials, Observe lets you see the token burn, Save keeps the user's "favourite worked example" across sessions, Guard checks that the OCR skill you just linked isn't hiding `child_process` landmines. This task is the most direct demo of *agent as middleware* **and** of *skill-as-first-class-capability*: the user hands over a picture and a natural-language request, the enabled skill's `description` is visible to the agent, the agent picks it up on its own, invokes the bundled scripts via `exec`, integrates the structured output, and returns a human-readable answer. No external OCR SaaS UI, no second browser tab, no copy-paste.

We chose PaddleOCR over a direct call to a vision LLM (Qwen3-VL, GLM-4V) because the WebUI composer attachment currently routes through the agent's `read` tool, which returns multimodal content but in practice the VL model hallucinates answers instead of reading the real pixels — we repeatedly got fabricated problems (`54-38=16`, `27+49=76`) from images that actually showed `23+47=70`. Wiring OCR explicitly gives a deterministic, auditable path where the agent's tool calls are visible in the chat (`exec ls` → `exec parse-document.mjs` → quote markdown → solve) and the cost is predictable.

See [README_CN.md](./README_CN.md) for the full 6-step walkthrough, the AI Studio endpoint setup, the comparison table between arithmetic and quadratic prompts, CLI cross-checks, and FAQ covering `Unrecognized key: "ocr"` from older openclaw CLIs, stale-memory-pollution making the agent skip the OCR skill, and the follow-on idea of bridging this to a Feishu/iMessage "photo in → answer out" channel.
