# Task: Compile a Handful of Sources into a Self-Maintaining Wiki

**Capability**: Save (core capability #3)
**Time**: ~10 minutes
**Level**: Beginner (after [wizard-ernie-glm](../../setup/wizard-ernie-glm/))

> Drop three related notes into Wiki → watch Wiki auto-tag them with freshness, lifecycle, and link signals → ask a cross-source question → save the good answer as a synthesis page with one click → run Evolve to let Wiki check itself and log health signals. **Then cross into the OpenClaw WebUI** and ask the chat agent about the same topic — watch it cite your just-ingested pages by title. The takeaway: **you provide the sources, Wiki compiles and maintains them; ClawMaster is where you curate, OpenClaw WebUI is where the agent uses it** — no manual filing, tagging, or re-reading.

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

![](./images/08-webui-answer.png)
*OpenClaw WebUI — the agent answers a Wiki-relevant question by citing the exact pages you wrote into Wiki (`Karpathy 的 LLM Wiki`, `RAG 与 Wiki 的对比`, `EVOLVE 自维护循环`, and the `synthesis-llm-rag` synthesis page). Injection is automatic via the PowerMem plugin's `before_agent_start` hook.*

## TL;DR

1. Open Wiki under the Workspace nav group (`/wiki` route, web mode only — the desktop Tauri build doesn't ship Wiki yet)
2. Paste three related Markdown notes into **Write source** and click **Write** — pure Markdown goes straight in, URLs hit a 4-button confirmation gate
3. Each ingested page gets auto-tagged: **fresh / just-ingested / source**, with `[[wikilinks]]` parsed into backlinks
4. Ask a cross-source question in **Ask Wiki**, then click **Save synthesis** to persist the answer as a new LLM-synthesis page
5. Click **Run evolve** — the "What Wiki has already done for you" counters jump: LLM-synthesis pages, auto-maintained pages, health signals
6. Jump from ClawMaster **Overview** into **OpenClaw WebUI**, ask a Wiki-relevant question (include words like "wiki" / "knowledge base" / "what do we know about…"), and watch the agent cite your ingested pages by title

## What this task shows

| You do | Wiki does automatically |
|---|---|
| Write 3 Markdown notes | Split into pages → tag freshness/lifecycle/type → resolve `[[wikilinks]]` → index into PowerMem |
| Ask one cross-source question | Retrieve relevant pages → synthesize answer → cite sources |
| Click "Save synthesis" | Create a `synthesis/` page → fill metadata → backlink to the sources |
| Click "Run evolve" | Recompute freshness → check source reachability → log evolution evidence |
| Ask in OpenClaw WebUI | Send a normal chat message | Auto-inject `<relevant-wiki>` context → agent cites pages by title → no manual paste needed |

Manual effort: 3 notes + 1 question + 2 button clicks + 1 WebUI chat. Everything else — classification, tagging, cross-referencing, synthesis, periodic maintenance, health checks, and handing the curated content to the agent at answer time — is Wiki's job.

See [README_CN.md](./README_CN.md) for the full walkthrough, the exact sample notes to paste, and the FAQ (nav-entry missing on desktop, `source` counter staying at 0 for manual-content pages, `[[link]]` resolution rules, where vault files live on disk).
