# Lab · 2026-04-25 · 深圳

> ClawMaster + OpenClaw 首场线下工作坊 — 面向非技术用户与 AI 开发者

## 活动信息

| 项目 | 内容 |
|---|---|
| **日期** | 2026 年 4 月 25 日（周六） |
| **时长** | 1 小时 |
| **地点** | 中国 · 深圳 |
| **主办方** | OceanBase × 百度 AI Studio |
| **面向对象** | 想把 OpenClaw 用在日常工作/生活中的产品经理、非技术用户；希望基于 OpenClaw 构建 Agent 的开发者 |
| **语言** | 中文（讲解）+ 中/英/日 三语资料 |

## 目标

参与者在本次 Lab 结束时，应该能：

1. 独立完成 ClawMaster 安装并把 **文心大模型（ERNIE）** 与 **智谱 GLM** 接入同一个 OpenClaw profile
2. 在控制台中查看一次对话的 **tokens 用量与估算成本**
3. 安装并启用至少一个**技能**（如 `ernie-image`、`paddleocr-doc-parsing`）

## 前置准备（到场前完成）

- [ ] 笔记本：**macOS 最佳**，Linux 其次；Windows 可跑但现场优先支持 macOS
- [ ] **Node.js 22+ LTS**（`node -v` 应显示 v22 或更高；Node 20 本月进入 maintenance，不推荐新装）+ **npm 10+**（ClawMaster 通过 `npm i -g clawmaster` 安装，Node 22 自带 npm 10）
  - 推荐：用 [nvm](https://github.com/nvm-sh/nvm) 或 [fnm](https://github.com/Schniz/fnm) 管理多版本，避免污染系统 Node
  - macOS 推荐 `brew install node@22` 或 `nvm install 22 && nvm use 22`；Windows 可直接装 [Node.js 22 LTS 官方安装包](https://nodejs.org/)
- [ ] 提前全局安装：`npm i -g clawmaster`
- [ ] 百度 AI Studio 令牌 **现场提供**（主办方统一发放，无需提前注册）；如已有自己的 key 也可直接带来使用

## 启动 ClawMaster（现场第一步）

本次 Lab 运行的是 **CLI + Web 混合模式** — 终端里跑一个 Node 进程，控制台 UI 通过浏览器打开。

```bash
clawmaster            # 等价于 clawmaster serve
```

**成功标志**：终端出现绿色 ASCII banner，紧接着一块 ready 块：

```
ClawMaster service ready.
web console: http://127.0.0.1:16223/?token=<随机 48 位>
bind:        127.0.0.1:16223
token:       <和上面 URL 里的 token 相同>
next:        Ctrl+C to stop
```

浏览器会**自动打开**那个带 `?token=` 的链接，落到**设置向导**第一步 — 这也就是 [动手 1](../../tasks/setup/wizard-ernie-glm/README_CN.md) 的起点。

**如果浏览器没自动弹**（Linux 无 `xdg-open` / 跑在 SSH / 带 `--silent`）：
- 手动把终端打印的 `web console:` 那一整条 URL（含 token）粘到浏览器即可
- token 会写到 `~/.clawmaster/service/service-state.json`，丢了可 `cat` 那个文件找回

**排错小抄**（终端不出 ready 块）：

| 现象 | 检查 |
|---|---|
| `command not found: clawmaster` | `npm i -g clawmaster` 没跑 / PATH 里没 npm 全局 bin（macOS 常见于 nvm 切版本后） |
| `EADDRINUSE: address already in use :::16223` | 上次进程没退：`clawmaster stop` 或 `lsof -ti:16223 \| xargs kill` |
| `Build assets are missing` | 你装的是源码不是 npm 包；跑 `clawmaster doctor` 对照一下 |
| 终端卡在 banner 后没 ready | 等 ~3 秒；还不行按 Ctrl+C 再 `clawmaster doctor` 看 node / openclaw / 构建产物是否齐 |

终端保持前台 — **整场 Lab 都不要关这个终端窗口**，Ctrl+C 会把服务一起关掉。想切后台可用 `clawmaster serve --daemon`，收工再 `clawmaster stop`。

## 当天任务流

| 时段 | 任务 | 目标 |
|---|---|---|
| 开场 | 介绍 ClawMaster + OpenClaw 的定位 | 理解「配置控制面 + 日用伴侣」的产品思路 |
| 动手 1 | [wizard-ernie-glm](../../tasks/setup/wizard-ernie-glm/README_CN.md) | 完成 ERNIE + GLM 的双提供商初始化 |
| 动手 2 | [powermem-takeover-file-memory](../../tasks/save/powermem-takeover-file-memory/README_CN.md) | 让 PowerMem 接管 OpenClaw 的文件式记忆：同步桥接 → 一键导入 `MEMORY.md` → 语义召回对比 |
| 动手 3 | [ernie-image-illustrated-article](../../tasks/build/ernie-image-illustrated-article/README_CN.md) | 一键启用 `content-draft` + `ernie-image`，把 LangChain DeepAgents 文档转成带配图的公众号草稿 |
| 答疑 | 自由 Q&A + 加入社区 | — |

> 具体任务会在本文件的「当天任务流」表格中持续更新，也会实时同步到现场大屏。

## 资源

- **主会场 Wi-Fi**：现场提供（SSID 会在签到台告知）
- **仓库**：<https://github.com/openmaster-ai/clawmaster-workshop>（本仓库）
- **主仓**：<https://github.com/openmaster-ai/clawmaster>

## 主办方致谢

感谢 **OceanBase** 与 **百度 AI Studio** 提供场地、算力与 Token 赞助，让本次工作坊得以成行。
