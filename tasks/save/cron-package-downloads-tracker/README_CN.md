# 任务：让 PowerMem 给 `package-download-tracker` 当时序存储，起一条周下载量 cron

**能力域**：Save + Cron · **用时**：~12 min · **难度**：进阶（建议先做完 [cron-cost-digest](../../observe/cron-cost-digest/README_CN.md) 和 [powermem-takeover-file-memory](../powermem-takeover-file-memory/README_CN.md)）

> 在技能目录里看到 `package-download-tracker` 这个内置技能 → 贴一个 URL 打开预填好的 cron 表单 → **立即运行** 一次，让 skill 抓 npm/PyPI 并把观测写进 PowerMem → 打开 WebUI 看 agent 的真实 tool 流水 → 去 PowerMem 里看到三条新记录。把 PowerMem 从「记事实」升级成「定时任务的时序后端」——虽然「上周」列真正填上数字得等一周，但今天就能看到种子已经种下去了。

> 🌐 English：[README.md](./README.md) · 日本語：[README_JP.md](./README_JP.md)

## 为什么要单独做这个任务

`clawprobe-cost-digest`（[cron-cost-digest](../../observe/cron-cost-digest/README_CN.md) 里那条 cron）每次跑都重读一遍 SQLite 重出 markdown，跟记忆无关。`package-download-tracker`（PR #119 带进来的内置 skill）不一样：

- 每次跑：打 `api.npmjs.org/downloads/range/...` 和 `pypistats.org/api/packages/...`，把当期快照存进本地 cache
- 之后通过 `openclaw ltm add` 把压缩后的观测（带 `snapshot-json:` 标记的 markdown 记录）写进 PowerMem
- 下一次跑：先 `openclaw ltm search` 捞匹配的 `(registry, package, period)` 观测，用它们当历史基线

这就是 skill 的 prompt 里反复强调的 "prefer stored history over repeated broad registry queries"。本任务要做两件事：**亲眼看 PowerMem 被当时序存储用**（而不是当事实索引），**搞清楚 npm 和 PyPI 为什么行为不一样**（一个靠 PowerMem，一个靠 API 本身）。

## 前置条件

1. ClawMaster 跑在 <http://localhost:16223>，网关徽标显示 **GATEWAY 运行中**
2. PowerMem 已经接管 OpenClaw 记忆（走完 [powermem-takeover-file-memory](../powermem-takeover-file-memory/README_CN.md)），`openclaw ltm list --json` 能正常返回
3. 模型下拉里有一个带 `provider/` 前缀、跑得起来的模型（本文用 `siliconflow/deepseek-ai/DeepSeek-V3`）
4. 还没跑过这个 skill（跑过了也行，但 PowerMem 里会有一堆旧观测，清掉更干净，见 FAQ）

---

## 第 1 步：在技能目录确认 `package-download-tracker` 已经装好

左侧导航 → **技能** → 向下滚到 **当前工作区已安装** 区域，找到：

![技能目录里高亮 package-download-tracker 卡片](./images/01-skills-catalog-pkg-card.png)

卡片里写的是 skill 的自描述：「Track npm and PyPI package downloads by week or month, use stored history observations, and save compact trend analysis for future package adoption questions.」这是 `~/.openclaw/workspace/skills/package-download-tracker/SKILL.md` 的 front-matter，`openclaw` 启动时扫出来的。

> ⚠️ 目前版本**没有** cron 模板 picker 这种 UI。你在 `/cron` 页面点「新建任务」只会拿到一个空白表单，没有「每周包下载追踪」卡片可点。真正的模板入口是 **URL 参数**：`/cron?template=package-downloads&period=week`。把它当 bookmark 用，或者 workshop 里直接贴给参与者。

---

## 第 2 步：用 URL 打开预填好的 cron 表单

浏览器地址栏贴：

```
http://localhost:16223/cron?template=package-downloads&period=week
```

页面上方会弹一个 toast「已加载周包下载追踪模板。请在提示词中修改包名、配置投递方式后保存。」同时新建任务 modal 自动打开，全部字段预填：

![cron 新建任务 modal，消息 + 模型两处用黄框标出](./images/02-cron-form-prefilled.png)

| 字段 | 预填值 |
|---|---|
| 名称 | `Weekly Package Download Tracker` |
| 说明 | `通过内置 package-download-tracker 技能每日检查包下载趋势。` |
| 消息 | 完整 skill prompt（npm 包 `clawmaster, powermem`、PyPI 包 `powermem` 作为样例；明确禁止向用户暴露 memory/cache/命令细节）|
| 会话 | `isolated` |
| Agent | `main` |
| 调度类型 | Cron 表达式 · `0 8 * * *`（每天 08:00——对周指标来说也是天天刷，一周内任何时候都能看到最新窗口） |
| 时区 | 浏览器时区 |

**唯一要手动改的两处**：

- **模型**（`openai/gpt-4.1` 占位符那格）：填一个带 `provider/` 前缀的（例 `siliconflow/deepseek-ai/DeepSeek-V3`）。没前缀 cron 起来会 `FailoverError: Unknown model`
- **消息** 里样例包名如果你想换成别的，保持 "npm packages \`xxx, yyy\`; PyPI packages \`zzz\`" 这个结构，prompt 其他约束（历史优先、缺失上周不编数字、不提内部机制）**不要删**——改短很容易连 `--load-memory --save-memory` 的逻辑一起漏掉

点右下 **新建任务** 保存。列表里新 cron 的下次运行是明早 08:00。

---

## 第 3 步：**立即运行** 一次，去 WebUI 看真实回复

点新建 cron 卡片上的 **立即运行**。isolated 会话启动，~30 秒后状态变 🟢 **ok**。

> ⚠️ 运行历史抽屉（点「运行记录」打开的那个）**只显示耗时和状态**——没有 markdown 表格。要看 agent 真正说了什么，必须点「在 WebUI 中打开」跳到 OpenClaw 的 chat 视图。

WebUI 里最后那个 **Tool 角色** 消息会长这样：

![WebUI 回复：npm 两包 n/a，PyPI 一包 956 vs 1212 = -21.1%](./images/03-webui-cold-reply.png)

表的形状（这是 skill 的 response expectation 硬要求）：

| 包 | Registry | 本周下载 | 上周下载 | 变化 |
|---|---|---:|---:|---|
| clawmaster | npm | 488 | n/a | 无上周数据 |
| powermem | npm | 511 | n/a | 无上周数据 |
| powermem | PyPI | 956 | 1,212 | ↓ 21.1% |

注意两件事：

1. **npm 的「上周」列是 n/a**。这是冷启动：PowerMem 里没有 npm 的历史观测，所以没有基线。skill 的 prompt 明确禁止在缺基线时伪造百分比（第 4 列对应的写「无上周数据」/「—」/「不可用」，**不能** 写 `+∞%`）
2. **PyPI 的「上周」列是真的数字**。这不是 PowerMem 帮忙算的，是 `pypistats.org/api/packages/powermem/recent` 这个接口本来就返回 `last_week` + `last_month` 两个窗口。所以 PyPI 在 **第一次跑就有 delta**；npm 得等 PowerMem 里攒出跨周观测才有

---

## 第 4 步：去 PowerMem 看 skill 刚写了什么

左侧导航 → **记忆**，向下滚到 **最近托管记忆** 区域：

![PowerMem 记忆页最近托管记忆里出现 package-download-tracker 观测](./images/04-powermem-fresh-observation.png)

skill 以这种三行格式存每条观测（是纯 markdown 文本，不是 YAML front-matter）：

```
package-download-tracker registry:npm package:powermem period:week
summary: powermem had 511 week downloads; No previous week data available.
snapshot-json: {"registry":"npm","packageName":"powermem","period":"week","fetchedAt":"2026-04-29T08:46:01.056Z","totalDownloads":511,"trend":{"direction":"unknown","basis":"current-window","text":"No previous week data available.","points":1},"warnings":[]}
```

第一行是 skill 的搜索 marker（下次 `--load-memory` 会用正则匹这一行），第二行人读得懂，第三行的 `snapshot-json:` 后面是一个完整 JSON——skill 其实只认 JSON 里的 `registry/packageName/period/fetchedAt/totalDownloads` 这几个字段，其他两行只是给人看的。

本次应该看到 **三条** 这样的记录：npm/clawmaster、npm/powermem、pypi/powermem。CLI 也能查：

```bash
openclaw ltm list --limit 10 --json | jq '.memories[] | select(.content | startswith("package-download-tracker "))'
```

> ⚠️ 同一页上方的 **最近网关捕获** 区里，你会看到一条「包周下载趋势追踪结果：...」的内容——那是 OpenClaw gateway 的 auto-capture 特性把 agent 的整段 markdown 回复也塞进了 PowerMem。它的 `metadata.source` 是 `openclaw-gateway-auto-capture`，**不** 是 skill 写的观测。skill 的 `--load-memory` 靠 `snapshot-json:` marker 过滤，所以这些自动捕获不会污染历史召回，但会让 PowerMem UI 里看起来条目变多。

---

## 第 5 步：再 **立即运行** 一次，理解为什么表没变

点第二次 **立即运行**。等 ~30 秒。打开 WebUI 看第二次的回复：

![同一张表，npm 依然 n/a，PyPI 依然 -21.1%](./images/05-webui-warm-reply.png)

**表跟第一次一模一样**。这是预期行为，不是 bug——三件事叠加：

1. **skill 按 `(registry, package, period)` 匹配历史**。你刚存的那条 `npm clawmaster period:week` 观测 `fetchedAt` 是今天（2026-04-29），skill 下次调用时 **当前窗口也是这一周**——这个快照既是「当前」也是「历史里唯一一条」，skill 代码里 `points: 1` 时 `basis` 回落成 `current-window`，不算有前一周基线
2. **npm 的 `/downloads/range/last-week/...` 接口只返回当前一周**，不像 pypistats 会附送上一周。所以在「PowerMem 里只有本周观测」的前提下，npm 永远是 n/a
3. **同一天再跑只会再插一条 `fetchedAt` 相近的观测**（你可以在第 4 步再跑一次 `openclaw ltm list --json` 验证），对 skill 来说还是「只有当前周的快照」

要 **真的** 看到 npm 的「上周」列填进去，有两条路：

- **老实等一周**：这条 cron 每天 08:00 跑。到下周一，PowerMem 里今天这批观测的 `fetchedAt` 已经落在「上一周」的日历周内，skill 会把它们当真正的基线，表里 npm 就会出数字了。这是 workshop 的正确叙事：「今天种下种子，下周看到芽」
- **现在就想看**：不推荐在 workshop 里演示，因为得手工编一条 `fetchedAt` 指向上周的假观测塞进 PowerMem，那是伪造数据，容易误导参与者

---

## 第 6 步：WebUI 里看 tool call 流，确认 agent 是怎么调 skill 的

WebUI 最左上点回第一次 run（或者运行历史抽屉里第一次那条）。展开最上面的 **Tool call** 卡片：

![WebUI tool 时间轴：sessions_spawn + sessions_yield 把活儿交给 subagent](./images/06-webui-tool-timeline.png)

**会看到两种模式**（是个模型变量，不是 skill 设计问题，写下来免得现场踩坑）：

- **好的路径**（DeepSeek V3 第一次跑我们看到的）：`session_status → sessions_spawn → sessions_yield`——agent 把具体工作交给一个 subagent，subagent 里才会 `read SKILL.md` + `exec track-downloads.mjs --registry npm --package ... --period week --load-memory --save-memory`。subagent 的会话在 WebUI 左侧 session 选择器里会显示成 `subagent:xxxx`，切过去能看到真正的 `exec` 调用
- **坏的路径**（第二次跑里出现的 `Tool package-download-tracker not found`）：agent 试图把 `package-download-tracker` 当内置 tool 直接调用——但它不是 tool 而是 **一套说明文档 + Node 脚本**。这种时候 skill 根本没重跑，agent 会从 `<relevant-memories>` 里把第一次跑的观测抄进回复，看起来像正常回复其实没做工

SKILL.md 第一段就在防这个：

> This skill is guidance plus runnable scripts, not a callable tool name.
> - Do not call `package-download-tracker` as if it were a built-in tool.
> - First use `read` to load this `SKILL.md`.
> - Then use `exec` to run the bundled Node script with `node`.

如果你发现 workshop 参与者的 cron 连跑三次都走「坏的路径」，把 cron 消息里那一段 `使用已安装的 package-download-tracker 技能...` 换成更强的 **positive instruction**：

```
先 read ~/.openclaw/workspace/skills/package-download-tracker/SKILL.md，再用 exec node 命令运行里面提到的 scripts/track-downloads.mjs，带上 --load-memory --save-memory --summary --period week，针对 npm packages clawmaster, powermem 和 PyPI packages powermem。
```

把负面约束（「不要 ... 不要 ...」）换成正面动作（「先 read...再 exec...」）——见 `feedback_skill_demo_prompt_shape` 记忆里那条建议。

---

## 验证块

UI：

- 第一次 run 后：WebUI 回复里 npm 列是 n/a、变化列是「无上周数据」；PyPI 列有数字 + 百分比
- `/memory` 页「最近托管记忆」里出现 3 条 `package-download-tracker registry:...` 记录
- WebUI 上的 tool 时间轴：至少有一次 `sessions_spawn` 调用（说明 agent 走的是 subagent 路径）

CLI：

```bash
# 刚写进去的观测数，期待 3 或更多（同一天多次跑会累加）
openclaw ltm list --limit 20 --json | grep -c 'package-download-tracker registry:'

# 手动跑 skill 本体——这是最干净的验证，不经过 agent，能直接看到 historyObservations 数组
node ~/.openclaw/workspace/skills/package-download-tracker/scripts/track-downloads.mjs \
  --registry npm --package clawmaster --period week --load-memory
# 注意 JSON 输出里 memory.snapshotsLoaded 字段：第一次跑是 0，第二次跑之后会 >0
```

cron 存储核对：

```bash
jq '.cronJobs[] | select(.name | contains("Package Download"))' ~/.openclaw/workspace/cron.json
# 或通过 CLI:
openclaw cron list --all --json | jq '.[] | select(.name | contains("Package Download"))'
```

---

## FAQ

**Q：第一次 run 的「本周下载」列是 0，skill 坏了？**
不一定。npm `/downloads/range/` 接口有 ~1 天的延迟，周一早上跑大概率抓不到本周周日的量；pypistats 也有小时级的滞后。skill 会在 JSON 的 `warnings` 里带警告，agent 应该把这个写进回复。如果 agent 吞了警告，把 `warnings` 数组在 SKILL.md 里的 "Response Expectations" 第 4 条强调一下。

**Q：「上周」列同一天就是不填，怎么办？**
本文第 5 步已经解释了——npm 是同一天跑无解的。等这条 cron 明天、后天继续跑，第 7 天开始 `fetchedAt` 已经跨周，npm 列就会开始出数字。如果你急着验证 PowerMem 召回链路本身能不能工作，走验证块里那条 `node ... --load-memory` 命令，JSON 输出里 `memory.snapshotsLoaded` 会告诉你召回了几条——只要 >0 说明链路通，跟表里「上周」列最终填不填是两回事。

**Q：agent 回复里把「我从 PowerMem 里找到上周的快照」也写出来了。**
prompt 里已经明确 "do not mention memory, stored snapshots, cache, script commands, or other internal mechanics"。如果模型还是漏，**不要** 往 prompt 里堆更多否定词（越堆越漏），改成把正例写具体：给一个两行的期望输出格式，参考 `feedback_skill_demo_prompt_shape` 里的原则。

**Q：第 6 步里那个 `Tool package-download-tracker not found` 到底严不严重？**
严重不严重取决于 agent 下一步怎么办。看 Assistant 最终那段文本：

- 如果最终表里数字和 PowerMem 里存的观测一致——说明 agent 走了 fallback 从 memory 里拼，**数据没重新算也没保存**，这次运行相当于白跑
- 如果表里数字跟一天前或更早的观测不一样，但 PowerMem 里没新增记录——一样是白跑，只是 agent 胡猜了新数字（这种情况 SKILL.md 第 7 条禁令专门在防，但防不住）

判断标准：**运行后 PowerMem 里「最近托管记忆」应该多出 N 条 `package-download-tracker registry:...`**（N = 你 prompt 里列的 npm 包数 + PyPI 包数）。少了就说明 skill 没真跑。

**Q：PowerMem 里堆了太多观测怎么办？**
skill 默认用 `--history-limit 6`——取最近 6 条。多出来的旧观测 skill 不会误用，但 PowerMem UI 里会显得乱。想清的话跑：

```bash
openclaw ltm list --limit 100 --json \
  | jq -r '.memories[] | select(.content | startswith("package-download-tracker registry:")) | .id' \
  | while read id; do
      curl -sS -X POST http://localhost:16223/api/memory/managed/delete \
        -H "content-type: application/json" -d "{\"memoryId\":\"$id\"}" > /dev/null
    done
```

这是 `feedback_powermem_auto_capture_pollution` 记忆里记录的 delete 接口。同样的手法也可以清掉 `openclaw-gateway-auto-capture` 的污染。

**Q：能不能追踪日报？**
不能。skill 的 `--period` 只接受 `week|month`（`isPackageDownloadPeriod` 在 `packages/web/src/shared/cronCostDigests.ts` 里硬编码）。想要日报得先改 skill（`--period day` + 相应的 API 调用）+ 同步改 cron 模板。

**Q：追踪私有 registry / 内网 npm？**
当前版本只走 `api.npmjs.org` 和 `pypistats.org`。要支持 `--registry custom --url https://...` 得改 skill。如果只是 npm private registry 的镜像，`api.npmjs.org` 对公开包照样工作，所以真正受限的是 **纯私有包**——这块 skill 目前是盲区。

---

## 下一步

- [math-quiz-vision-webui](../../apply/math-quiz-vision-webui/README_CN.md)：同样走 cron + skill，但数据源是 OCR 而不是 registry
- 自己写一条 skill：把 PowerMem 当时序存储这个套路拿去追踪 GitHub star、Docker pull、PyPI 单包细分版本、或任何能分页取当前窗口的数据源。关键是把 snapshot 序列化成带 `snapshot-json: {...}` 的纯文本，skill 自己维护 search marker + 去重逻辑
