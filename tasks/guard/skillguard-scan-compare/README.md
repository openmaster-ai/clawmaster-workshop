# Task: Compare-scan One Clean and One Risky Skill with SkillGuard

**Capability**: Guard (core capability #6)
**Time**: ~6 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> On the ClawMaster **Skills** page, scan two locally-installed skills side by side: `linkedin-content` (A · 0 findings) vs `baoyu-url-to-markdown` (C · 7 findings — 2× HIGH `exec(`, 4× MEDIUM `innerHTML =`, 1× LOW missing `allowed-tools`). One click on each teaches you exactly what SkillGuard is looking for and why dropping any community skill into your agent without auditing is risky.

> 🌐 This task was authored first in 中文. The complete walkthrough — 3 key frames, CLI cross-check, FAQ on what static analysis misses — lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## TL;DR

1. Left nav → **Skills**. Filter to `linkedin-content`, click **Scan**. Wait ~30–60s (first run pulls `@clawmaster/skillguard-cli` from npm). Panel shows **A · no findings · score 0**.
2. Click **View details** on the A-grade panel — same drawer component, but the four summary tiles read `A · Safe / score 0 / findings 0 / top severity LOW` (baseline placeholder, not a real LOW finding), the main panel renders a green **No findings** badge, and the right sidebar shows `Critical 0 · High 0`. Knowing what A looks like in detail is half the comparison.
3. Filter to `baoyu-url-to-markdown`, click **Scan**. Panel turns amber: **C · 7 findings · HIGH 2 · score 42**. Click **View details** — same drawer, but now the findings list has three MEDIUM `innerHTML =` entries with file paths + line numbers + Chinese/English remediation, and the sidebar shows `Critical 0 · High 2 · MEDIUM 4`.
4. Run the CLI for the full 7-finding list (the drawer only renders top 3): `npm exec --yes @clawmaster/skillguard-cli -- ~/.openclaw/skills/baoyu-url-to-markdown --json`.

## Why these two

- **linkedin-content** is pure markdown + frontmatter (writing-style guide, no code). If it had `allowed-tools` declared or didn't invoke any tools, it clears the least-privilege check — this is what A-grade looks like when it's honest.
- **baoyu-url-to-markdown** drives a headless Chrome via CDP, cleans HTML with Defuddle, and converts to markdown. That shape legitimately needs `innerHTML =` (DOM scraping) and sometimes `exec(` (adapter dispatch) — the point of Guard isn't that the skill is bad, it's that *you should know* the skill touches those APIs before the skill ships into your agent's default tool set.

The contrast between A and C with the same one-click flow is the demo.

## What SkillGuard does (and doesn't) catch

Static analysis against pattern rules:

- **Code Security (CWE / OWASP)**: `eval(`, `new Function`, `child_process.exec`, `innerHTML =`, unsafe dynamic imports
- **Least Privilege (Google SAIF)**: missing `allowed-tools` frontmatter → skill gets *all* tools by default
- Token budget estimates (L1/L2-eager/L2-lazy/L3) so you can see how much context the skill will consume when loaded

What it **won't** catch (document it as you hand the skill to users):

- Runtime-assembled code (`new Function(someObfuscated)`), network-fetched payloads, or logic inside external binaries the skill shells out to (e.g. `baoyu-fetch CLI`).
- For those you need OpenClaw's `allowed-tools` + exec-policy sandbox + ClawMaster channel approvals — SkillGuard is the first gate, not the only one.

See [README_CN.md](./README_CN.md) for the full 3-step walkthrough with screenshots, the direct `curl /api/skills/scan` cross-check, an explanation of why the drawer shows only the top 3 findings (and how to get all 7 via the CLI), and an FAQ covering scans that hang on first-run npm pull, `Installed skill directory not found`, and how to bolt Guard onto CI.
