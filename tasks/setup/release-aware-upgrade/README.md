# Task: Discover a New Release and Upgrade ClawMaster

**Capability**: Setup (core capability #1)
**Time**: ~5 minutes
**Level**: Beginner (after [wizard-ernie-glm](../wizard-ernie-glm/))

> When a new ClawMaster release is out, the app lights up an amber banner at the top. One click opens the Settings page where you can read the release notes and download the right installer for your OS — no need to dig around on GitHub. Version detection still works on networks that can't reach GitHub, and the installer choice is picked for your platform automatically.

> 🌐 This task was authored first in 中文. The full walkthrough lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## Key frames

![](./images/01-banner.png)
*An amber update banner at the top of the ClawMaster dashboard when a new release is available.*

![](./images/02-release-section.png)
*Settings → ClawMaster Releases: current/latest version cards, an orange "update available" note, and rendered release notes (headings, bullets, command blocks).*

![](./images/04-npm-fallback.png)
*When GitHub isn't reachable, the section still detects the new version (via npm), but switches the source line to a fallback note and replaces the release notes with a pointer to the full release page.*

![](./images/05-up-to-date.png)
*When you're already on the latest release: both version cards match and a green "ClawMaster is up to date" indicator replaces the amber one.*

## TL;DR

1. Open ClawMaster — an amber banner at the top says **"A ClawMaster v{version} update is available"** → click **View update**
2. Land on the **ClawMaster Releases** block under Settings
3. Read the release notes (rendered as real Markdown — headings, bullets, code blocks, not raw `#` or backticks)
4. Click **Open installer** for the right file for your OS, or **Changelog** to see the full release page on GitHub
5. Dismissing the banner hides it for that version only — a newer release brings it back

## Why this matters

Before v0.4.0, the only update prompt inside ClawMaster was for the command-line engine underneath. You had no way of knowing the **desktop / web app itself** had a new version — you had to check GitHub manually. This task surfaces the app's own updates in-product, with rendered release notes and a one-click installer for your exact OS, and falls back to an alternate source when GitHub isn't reachable so users on restricted networks still get notified.

## What you get

- **Proactive banner** — amber update bar shows up automatically when the app detects a newer release
- **In-context release notes** — read what changed without leaving ClawMaster
- **Platform-aware download** — macOS gets `.dmg`, Windows gets `.msi`, Linux gets `.AppImage`; one click, right file
- **Mirror-friendly detection** — works even when GitHub is slow or unreachable, honoring the npm mirror setting in Settings → Capabilities
- **One-version dismissal** — banner closes for *that* version only, so future releases still get your attention

See [README_CN.md](./README_CN.md) for the full step-by-step, contrast with the up-to-date state, and the FAQ covering stuck banners after upgrading, raw Markdown in the notes panel, blocked installer links, and mirror-speed troubleshooting.
