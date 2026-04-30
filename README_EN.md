<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/dark/horizontal.png" />
    <img src="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/white/horizontal.png" width="100%" alt="ClawMaster" />
  </picture>
</p>

<p align="center"><strong>ClawMaster · Workshop</strong></p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster"><img src="https://img.shields.io/badge/%F0%9F%A6%9E%20Main%20repo-ClawMaster-111111?style=for-the-badge" alt="Main repo: ClawMaster" /></a>
  <img src="https://img.shields.io/badge/You%20are%20here-Workshop-0A7EA4?style=for-the-badge" alt="You are here: Workshop" />
</p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster/releases"><img src="https://img.shields.io/github/v/release/openmaster-ai/clawmaster?label=Tracks%20ClawMaster&color=F5A623&style=for-the-badge" alt="Tracks ClawMaster latest release" /></a>
  <a href="https://github.com/openmaster-ai/clawmaster/milestone/1"><img src="https://img.shields.io/badge/Next%20milestone-v0.4.0-6f42c1?style=for-the-badge" alt="Next milestone: v0.4.0" /></a>
</p>

A learning repo for ClawMaster + OpenClaw, full of hands-on Labs and Tasks grounded in real scenarios. The main repo lives at [openmaster-ai/clawmaster](https://github.com/openmaster-ai/clawmaster); this one ships the hands-on companion.

> 🌐 中文（默认）：[README.md](./README.md) · 日本語：[README_JP.md](./README_JP.md)
>
> The first in-person event is in **Shenzhen, China**, so 中文 is the canonical language for this repo. EN / JP copies exist so readers elsewhere can skim the schedule and task index.

## Repo layout

```
clawmaster-workshop/
├── labs/              # Lightweight event records (date + location + host)
│   └── 20260425-shenzhen/
└── tasks/             # Reusable step-by-step walkthroughs, grouped by the 6 core capabilities
    ├── setup/         # ① Install, profiles, providers, channels
    ├── observe/       # ② Cost, token usage, gateway health
    ├── save/          # ③ Memory workspaces, LLM wiki
    ├── apply/         # ④ OCR, photo → flashcards, doc pipelines
    ├── build/         # ⑤ Skills, plugins, MCP, agent orchestration
    └── guard/         # ⑥ Skill audits, API-key vaults, usage caps
```

**Lab** is a specific event (date + location + host) that selects a set of tasks as its agenda.
**Task** is a reusable tutorial any Lab can pull from.

Labs ship a single `README.md` **in the host-location language** (Shenzhen = zh, Tokyo = jp) — they serve the live audience, nothing else. Tasks ship `README.md` (English) / `README_CN.md` / `README_JP.md` plus `images/` key-frames so any Lab, anywhere, can pull them in verbatim.

## Scheduled labs

| Date | Location | Host | Task list |
|---|---|---|---|
| 2026-04-25 | [Shenzhen](./labs/20260425-shenzhen/) 🇨🇳 zh | OceanBase × Baidu | [Setup / wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/) → [Save / powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/) → [Build / ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/) |

## Task index

### ① Setup
- [wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/) — configure ERNIE + GLM through the setup wizard and pick a default model
- [release-aware-upgrade](./tasks/setup/release-aware-upgrade/) — ClawMaster v0.4.0 release notifications: top-bar banner → Settings "ClawMaster Releases" section renders the release notes, auto-picks an installer by platform; GitHub → npm-mirror fallback

### ② Observe
- [cron-cost-digest](./tasks/observe/cron-cost-digest/) — install ClawProbe, one-click the Observe page's *Daily digest* preset into a cron, then **Run now** to close the Observe × Cron loop

### ③ Save
- [powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/) — have PowerMem take over OpenClaw's file-based memory: bridge sync → one-click import of `MEMORY.md` → side-by-side semantic recall
- [cron-package-downloads-tracker](./tasks/save/cron-package-downloads-tracker/) — treat PowerMem as a time-series store: the `package-download-tracker` cron fetches weekly npm / PyPI download counts, writes observations to PowerMem, and next week's run reads them as baseline

### ④ Apply
- [math-quiz-vision-webui](./tasks/apply/math-quiz-vision-webui/) — jump from Overview into the OpenClaw WebUI, wire up PaddleOCR, let the agent OCR a handwritten math quiz and compute the answers (arithmetic + quadratic samples)

### ⑤ Build
- [ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/) — enable `content-draft` + `ernie-image` and turn the LangChain DeepAgents docs into an illustrated WeChat-Official draft

### ⑥ Guard
- [skillguard-scan-compare](./tasks/guard/skillguard-scan-compare/) — on the Skills page, scan one clean skill (`linkedin-content` · A) and one risky skill (`baoyu-url-to-markdown` · C · 7 findings) back-to-back to see what SkillGuard is actually looking at

## Contributing a new task

1. Pick a capability under `tasks/` (or open a new one)
2. Create `tasks/<capability>/<slug>/` containing `README.md` + `README_CN.md` + `README_JP.md` + `images/`
3. The walkthrough should:
   - **Lead with the scenario** — state up front what the reader gets at the end
   - **Drive on screenshots** — one key frame per major state change
   - **Run end-to-end** — commands must be copy-pasteable, no "fill this in yourself" gaps
4. Open a PR — CI checks Markdown conventions and that all three locales exist

## License

Apache-2.0, matching the main ClawMaster repo.
