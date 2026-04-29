<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/dark/horizontal.png" />
    <img src="https://raw.githubusercontent.com/openmaster-ai/brand/main/logos/clawmaster/wordmarks/white/horizontal.png" width="100%" alt="ClawMaster" />
  </picture>
</p>

<p align="center"><strong>龙虾管理大师 · 工作坊</strong></p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster"><img src="https://img.shields.io/badge/%F0%9F%A6%9E%20Main%20repo-ClawMaster-111111?style=for-the-badge" alt="Main repo: ClawMaster" /></a>
  <img src="https://img.shields.io/badge/You%20are%20here-Workshop-0A7EA4?style=for-the-badge" alt="You are here: Workshop" />
</p>

<p align="center">
  <a href="https://github.com/openmaster-ai/clawmaster/releases"><img src="https://img.shields.io/github/v/release/openmaster-ai/clawmaster?label=Tracks%20ClawMaster&color=F5A623&style=for-the-badge" alt="Tracks ClawMaster latest release" /></a>
  <a href="https://github.com/openmaster-ai/clawmaster/milestone/1"><img src="https://img.shields.io/badge/Next%20milestone-v0.4.0-6f42c1?style=for-the-badge" alt="Next milestone: v0.4.0" /></a>
</p>

面向真实场景的 ClawMaster + OpenClaw 学习仓库，内含可动手跑通的实验（Lab）与任务（Task）。主仓在 [openmaster-ai/clawmaster](https://github.com/openmaster-ai/clawmaster)；本仓是配套的动手教程。

> 🌐 English：[README_EN.md](./README_EN.md) · 日本語：[README_JP.md](./README_JP.md)
>
> 首场线下活动在**中国深圳**举办，默认文档语言是中文；EN / JP 版用于参考排期和检索任务索引。

## 仓库结构

```
clawmaster-workshop/
├── labs/              # 轻量的活动记录（时间 + 地点 + 主办方）
│   └── 20260425-shenzhen/
└── tasks/             # 可复用的步骤化教程，按 6 大核心能力分组
    ├── setup/         # ① 引导安装、Profile、Provider、通道
    ├── observe/       # ② 成本、Token 用量、网关健康度
    ├── save/          # ③ 记忆工作区、LLM Wiki
    ├── apply/         # ④ OCR、照片 → 闪卡、文档流水线
    ├── build/         # ⑤ 技能、插件、MCP、Agent 编排
    └── guard/         # ⑥ 技能审计、API Key 保险箱、用量上限
```

**Lab（实验）** 是一次具体的活动（时间 + 地点 + 主办方），它会选取一组任务作为当天的行程。
**Task（任务）** 是一份可复用的教程，任何 Lab 都可以引用。

Lab 只提供**举办地所在地区的语言**版本 `README.md`（如深圳场 = 中文、东京场 = 日文），专注服务现场听众；Task 则提供 `README.md`（英文）、`README_CN.md`（中文）、`README_JP.md`（日文）三语 + `images/` 关键帧截图，任何语种的 Lab 都能直接引用。

## 已排期 Lab

| 日期 | 地点 | 主办方 | 任务清单 |
|---|---|---|---|
| 2026-04-25 | [深圳](./labs/20260425-shenzhen/) 🇨🇳 中文 | OceanBase × 百度 | [Setup / wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/README_CN.md) → [Save / powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/README_CN.md) → [Build / ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/README_CN.md) |

## 任务索引

### ① Setup（初始化）
- [wizard-ernie-glm](./tasks/setup/wizard-ernie-glm/README_CN.md) — 通过设置向导配置 ERNIE + GLM，选定默认模型

### ② Observe（可观测）
- [cron-cost-digest](./tasks/observe/cron-cost-digest/README_CN.md) — 装好 ClawProbe，用可观测页「日报摘要」模板一键生成成本摘要 cron，**立即运行** 看闭环

### ③ Save（记忆）
- [powermem-takeover-file-memory](./tasks/save/powermem-takeover-file-memory/README_CN.md) — 让 PowerMem 接管 OpenClaw 的文件式记忆：同步桥接 → 一键导入 `MEMORY.md` → 语义召回对比
- [cron-package-downloads-tracker](./tasks/save/cron-package-downloads-tracker/README_CN.md) — 把 PowerMem 当时序存储用：`package-download-tracker` cron 每周抓 npm / PyPI 下载量，观测写进 PowerMem 给下周跑的自己当基线

### ④ Apply（使用）
- [math-quiz-vision-webui](./tasks/apply/math-quiz-vision-webui/README_CN.md) — 从概览页跳进 OpenClaw WebUI，配好 PaddleOCR，让 agent 自己 OCR 手写数学题再给出正确答案（算术 + 二次函数两个样本）

### ⑤ Build（构建）
- [ernie-image-illustrated-article](./tasks/build/ernie-image-illustrated-article/README_CN.md) — 启用 `content-draft` + `ernie-image`，把 LangChain DeepAgents 文档转成带配图的公众号草稿

### ⑥ Guard（安全）
- [skillguard-scan-compare](./tasks/guard/skillguard-scan-compare/README_CN.md) — 在技能页分别扫一条干净（`linkedin-content` · A）和一条有风险（`baoyu-url-to-markdown` · C · 7 findings）的技能，看 SkillGuard 看的是什么

## 贡献新任务

1. 在 `tasks/` 下选一个能力域（或新开一个）
2. 建立 `tasks/<能力>/<任务名>/` 目录，包含 `README.md` + `README_CN.md` + `README_JP.md` + `images/`
3. 教程应当满足：
   - **场景优先** — 先说这个任务做完能得到什么
   - **截图驱动** — 每个关键动作一张关键帧
   - **可直接跑通** — 命令要能复制粘贴，别留「自己补」的空档
4. 发起 PR — CI 会检查 Markdown 规范与三语文件是否齐备

## License

Apache-2.0，与 ClawMaster 主仓保持一致。
