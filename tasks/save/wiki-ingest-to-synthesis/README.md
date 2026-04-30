# Task: Compile a Handful of Sources into a Self-Maintaining Wiki

**Capability**: Save (core capability #3)
**Time**: ~10 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Drop three related notes into Wiki → watch Wiki auto-tag them with freshness, lifecycle, and link signals → ask a cross-source question → save the good answer as a synthesis page with one click → run Evolve to let Wiki check itself. **Then cross into the OpenClaw WebUI** and ask the agent to "draft a 300-word blog post on X" — you don't mention "wiki" or paste any links, but the draft clearly carries the opinions you already compiled. The takeaway: **you provide the sources, Wiki compiles and maintains them; ClawMaster is where you curate, OpenClaw WebUI is where the agent uses your curated content in everyday chat** — no manual filing, tagging, or re-feeding source material.

> 🌐 This task was authored first in 中文. The full walkthrough lives at **[README_CN.md](./README_CN.md)**. 日本語版：[README_JP.md](./README_JP.md)

## Key frames

![](./images/07-hero.png)
*End state: 4 pages / 0 stale at the top; bonus block shows 1 LLM synthesis, 3 source pages, 4 maintenance checks, 0 health signals. The list below includes the generated synthesis page.*

![](./images/02-url-confirm.png)
*URL ingestion gate — Wiki refuses to silently persist external links. Four explicit choices: write URL / summarize once / conversation-only / cancel.*

![](./images/04-detail.png)
*Page detail modal with origin banner (source / LLM-synthesis / maintained) + evolution banner (changed vs. just-checked) + markdown preview with clickable `[[wikilinks]]` + metadata grid.*

![](./images/05-query.png)
*Ask Wiki: answer synthesized from the ingested pages plus a "save as synthesis page" offer, which turns a one-off Q&A into a reusable page.*

![](./images/08-overview-jump.png)
*Starting point — the **Open OpenClaw WebUI** button (amber-highlighted) in ClawMaster's Overview page launches the agent chat in a new tab with the gateway token already attached.*

![](./images/09-webui-blog.png)
*End-user prompt in WebUI: "draft a 300-word WeChat post about why LLM Wiki beats RAG for long-term knowledge work." The user didn't mention "wiki" and didn't paste any source links, yet phrases like "extract concepts, flag contradictions, build cross-references" come straight from the `Karpathy 的 LLM Wiki` page, and the tagline "RAG is good at finding things, Wiki is good at growing them" is synthesized from `RAG 与 Wiki 的对比`. Injection is automatic via the PowerMem plugin's `before_agent_start` hook.*

![](./images/10-webui-scenarios.png)
*Follow-up in the same session: "list three typical scenarios for LLM Wiki, 50 chars each." The agent produces three scenarios — personal / team / product docs — each anchored in the compiled Wiki pages, continuing the thread without the user reintroducing any background.*

![](./images/11-webui-recall.png)
*Recall-style question in a fresh session: "did we discuss how EVOLVE solves the unmaintained-knowledge-base problem?" The agent opens with "Yes, the relevant-memories just injected this" and recites the three mechanisms (Ebbinghaus decay / auto-refresh / feedback-driven) verbatim from the `EVOLVE 自维护循环` page.*

## TL;DR

1. Open Wiki under the Workspace nav group (`/wiki` route, web mode only — the desktop Tauri build doesn't ship Wiki yet)
2. Paste three related Markdown notes into **Write source** and click **Write** — pure Markdown goes straight in, URLs hit a 4-button confirmation gate
3. Each ingested page gets auto-tagged: **fresh / just-ingested / source**, with `[[wikilinks]]` parsed into backlinks
4. Ask a cross-source question in **Ask Wiki**, then click **Save synthesis** to persist the answer as a new LLM-synthesis page
5. Click **Run evolve** — the "What Wiki has already done for you" counters jump: LLM-synthesis pages, auto-maintained pages, health signals
6. Jump from ClawMaster **Overview** into **OpenClaw WebUI**, send a natural end-user request like "**draft me a short blog post about X**" — no need to mention "wiki" or paste links; the agent auto-pulls your ingested pages as context and the draft carries your compiled opinions

## What this task shows

| You do | Wiki does automatically |
|---|---|
| Write 3 Markdown notes | Split into pages → tag freshness/lifecycle/type → resolve `[[wikilinks]]` → index into PowerMem |
| Ask one cross-source question | Retrieve relevant pages → synthesize answer → cite sources |
| Click "Save synthesis" | Create a `synthesis/` page → fill metadata → backlink to the sources |
| Click "Run evolve" | Recompute freshness → check source reachability → log evolution evidence |
| Ask OpenClaw to "draft a blog post about X" | Natural chat, no wiki mention | Auto-inject `<relevant-wiki>` context → agent writes using your compiled opinions → no manual paste needed |

Manual effort: 3 notes + 1 question + 2 button clicks + 1 natural chat request. Everything else — classification, tagging, cross-referencing, synthesis, periodic maintenance, health checks, **and feeding your curated opinions into the agent at generation time** — is Wiki's job.

See [README_CN.md](./README_CN.md) for the full walkthrough, the exact sample notes to paste, and the FAQ (nav-entry missing on desktop, `source` counter staying at 0 for manual-content pages, `[[link]]` resolution rules, where vault files live on disk).
