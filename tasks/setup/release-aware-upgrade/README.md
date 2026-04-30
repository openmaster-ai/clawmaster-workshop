# Task: Discover and Install a New ClawMaster Release

**Capability**: Setup (core capability #1)
**Time**: ~5 minutes
**Level**: Beginner (after [wizard-ernie-glm](../wizard-ernie-glm/))

> Starting in v0.4.0, ClawMaster actively detects its own new releases: a top-bar banner → the **ClawMaster Releases** section in Settings renders the release notes → the **Open installer** button auto-resolves to the right asset for your OS. GitHub Releases is the primary source; npm metadata (honoring the configured npm mirror) is the fallback.

> 🌐 This task was authored first in 中文. The full walkthrough lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## Key frames

![](./images/01-banner.png)
*Dashboard with the amber ClawMaster update banner at the top: "发现可用的 ClawMaster v0.5.0 更新。" + **View update** button.*

![](./images/02-release-section.png)
*Settings → ClawMaster Releases, `hasUpdate: true` state: current/latest version tiles, orange "ClawMaster v0.5.0 可以安装" indicator, GitHub source line, Markdown-rendered release notes.*

![](./images/04-npm-fallback.png)
*Same section when GitHub is unreachable — version is resolved from `npm distTags.latest`, source line switches to "已通过 npm 回退源检测到版本", release-notes panel degrades to a fallback line.*

![](./images/05-up-to-date.png)
*Contrast: current == latest. The banner doesn't render; the section shows a green "ClawMaster 已是最新版本" indicator.*

## TL;DR

1. Top-bar amber banner **"A ClawMaster v{version} update is available."** — click **View update**
2. Land on **Settings → ClawMaster Releases**. For a GitHub-sourced release, the section renders the release notes as real Markdown (headings, lists, fenced code), not raw `#` / ``` ``` ```
3. **There is no installer dropdown.** `selectInstallerAsset(latestRelease, navigator.platform)` auto-picks a single asset (macOS → `.dmg`, Windows → `.msi`, Linux → `.AppImage`); clicking **Open installer** opens that asset's GitHub URL. The secondary **Changelog** button goes to the release's `html_url`.
4. Dismissals persist per **version** in localStorage under `clawmaster-release-dismissed:{version}` — the banner won't return until GitHub publishes a newer release

## Why this matters

Before v0.4.0 the only update affordance in the app was for the **OpenClaw CLI**. ClawMaster itself had no visible upgrade path, so users on older desktop/web builds stayed behind. Issue #129 / PR #131 added a first-class release-detection surface with GitHub as the rich source, an npm fallback that respects the configured mirror (important for China-mainland users who can't reach `api.github.com`), and a dismissible banner so repeat nags don't train users to ignore the signal.

## Key mechanics

- **Current version**: Vite's `define` injects `__CLAWMASTER_VERSION__` from `package.json#version` into the bundle; read as `CLAWMASTER_VERSION` in `src/lib/appVersion.ts`
- **Latest version**: `fetch('https://api.github.com/repos/openmaster-ai/clawmaster/releases?per_page=10')` with a 3-second `AbortController`; on timeout/failure the adapter falls back to `GET /api/npm/clawmaster-versions` on the backend (or `list_clawmaster_npm_versions` via Tauri on desktop)
- **Markdown rendering**: release body is sanitized before render; `<script>` and external images are stripped; preview is capped at 700 chars
- **Dismissal**: persisted per version in localStorage (key prefix `clawmaster-release-dismissed:`); a new release ⇒ new key ⇒ banner reappears

See [README_CN.md](./README_CN.md) for the full walkthrough with verification commands (`curl` against GitHub, `npm view` with a mirror, localStorage inspection) and the FAQ covering the "banner stuck in dev mode", "notes shown as raw markdown", "Tauri shell `open` plugin allowlist", "`navigator.platform` empty string", and "mirror slow despite `--registry`" cases.
