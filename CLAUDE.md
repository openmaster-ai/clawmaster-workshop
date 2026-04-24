# CLAUDE.md

Conventions for working in the `clawmaster-workshop` repo. Read this before adding or editing labs, tasks, or screenshots.

## Repo layout

```
labs/<YYYYMMDD-loc>/     # one event per folder — lightweight metadata
tasks/<capability>/<slug>/  # reusable walkthroughs, grouped by the 6 core capabilities
  ├── README.md          # English
  ├── README_CN.md       # 中文
  ├── README_JP.md       # 日本語
  └── images/            # key-frame screenshots, numbered 01-NN
```

**Capabilities** are: `setup`, `observe`, `save`, `apply`, `build`, `guard`.

## Naming

- **Lab folder**: `YYYYMMDD-location` — no dashes inside the date prefix. Example: `20260425-shenzhen` (not `2026-04-25-shenzhen`).
- **Task slug**: scenario-descriptive, not sequential. Example: `wizard-ernie-glm`, `cron-cost-digest-1min` (not `task-01`).

## i18n policy

- **Labs** ship a **single `README.md` in the host-location language only** (Shenzhen → zh, Tokyo → jp). Don't create EN/JP copies for a location that has no such event scheduled. The root `README.md` follows the same rule — CN-only until a non-CN event is planned.
- **Tasks** ship **all three locales** (`README.md` / `README_CN.md` / `README_JP.md`) so any lab can reuse them. If one locale is the authoring language and the others are stubs, make the stubs a TL;DR + pointer to the full version — don't fake parity.

## Task walkthrough structure

1. Front-matter table: capability, time estimate, level, prerequisites (prior tasks)
2. "You'll learn" bullets — outcomes, not features
3. Step-by-step body, **one key-frame screenshot per major state change**
4. Verification block: UI path + CLI command + config-file `jq` check
5. "Next" pointers (related tasks, follow-up scenarios)
6. Troubleshooting FAQ

## Screenshot quality gate

**Never commit a screenshot showing a loading skeleton or blank state.** Audit every PNG before committing:

```python
from PIL import Image, ImageStat
im = Image.open(path).convert('RGB')
w, h = im.size
ctr = sum(ImageStat.Stat(im.crop((w//4, h//4, 3*w//4, 3*h//4))).stddev) / 3
# >20 solid · 12-20 thin (OK for modal dialogs) · <12 reject
```

Modal dialogs legitimately score ~19-20 because form fields are whitespace. Re-measure with a dialog-aware crop — `im.crop((w*0.08, h*0.05, w*0.95, h*0.95))` — and it should clear 20.

## Capture technique (dev-browser skill)

- **Force zh UI**: `page.addInitScript(() => localStorage.setItem('clawmaster-language', 'zh'))` **before** `goto`.
- **Localstorage sticks across pages in a session.** If a previous capture clicked the JP language button, new pages inherit JP. Spawn a fresh named page if you see unexpected locale.
- **Force the setup wizard**: `page.route('**/api/config', ...)` returning `{ models: { providers: {} } }`. Anything richer (meta, agents.defaults, plugins) trips `isOnboardingEnvironmentReady` and renders the dashboard instead.
- **Fake API-key validation**: intercept `**/api/system/probe-http` and return `{ success: true, data: { status: 200 } }`. Key probes go through the backend, not the provider.
- **Cron dialog schedule-type is a `<select>`, not a button.** Use `selectOption({ label: '单次任务' })`, not `getByRole('button')`.

## When in doubt

- Check `~/.claude/projects/-Users-haili-workspaces-clawmaster/memory/project_workshop_conventions.md` for the longer version
- Check the sibling repo `../clawmaster/` for ClawMaster UI source when a locale key or route isn't clear
