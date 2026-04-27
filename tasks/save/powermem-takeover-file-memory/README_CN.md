# 任务：让 PowerMem 接管 OpenClaw 的文件式记忆

**能力域**：Save · **用时**：~10 min · **难度**：入门（需先做 [wizard-ernie-glm](../../setup/wizard-ernie-glm/README_CN.md)）

> 同步 OpenClaw 桥接 → 一键导入 `~/.openclaw/workspace/MEMORY.md + memory/*.md` → 直接在 UI 写一条新事实 → 跑一次语义搜索 → 让 OpenClaw agent 在新会话里把这些记忆召回出来。整个过程展示 PowerMem 为什么比文件式记忆更好用。

> 🌐 English：[README.md](./README.md) · 日本語：[README_JP.md](./README_JP.md)

## 前置条件

1. 已完成 [wizard-ernie-glm](../../setup/wizard-ernie-glm/README_CN.md)，配置里已有可用的 provider
2. OpenClaw workspace 里已经有文件式记忆。没有就跑一条命令先种几个：

   ```bash
   mkdir -p ~/.openclaw/workspace/memory
   cat > ~/.openclaw/workspace/MEMORY.md <<'EOF'
   # 长期记忆索引
   - [用户角色](memory/user_role.md) — 深圳线下工作坊组织者
   - [写作偏好](memory/writing_style.md) — 中文技术内容，偏工程实证
   - [活动目标](memory/workshop_goal.md) — 下一场工作坊的目标和受众
   EOF
   cat > ~/.openclaw/workspace/memory/user_role.md <<'EOF'
   ---
   name: 用户角色
   type: user
   ---
   用户叫 Haili，常驻深圳，主要身份是 ClawMaster + OpenClaw 线下工作坊的组织者。目标受众是产品经理和 AI 开发者。
   EOF
   cat > ~/.openclaw/workspace/memory/writing_style.md <<'EOF'
   ---
   name: 写作偏好
   type: feedback
   ---
   文章要短，先给结论再给数据，不要把"新特性"当亮点写。偏好中文技术社区语气，避免过度营销。
   EOF
   cat > ~/.openclaw/workspace/memory/workshop_goal.md <<'EOF'
   ---
   name: 活动目标
   type: project
   ---
   下一场线下工作坊的目标是让 20 位参与者在 1 小时内完成 ClawMaster 安装、ERNIE/GLM 接入、PowerMem 接管。地点深圳，日期 2026-04-25。
   EOF
   ```

---

## 第 1 步：打开记忆页，确认桥接

左侧导航 → **记忆**。页面顶部是健康汇总，下方是 **PowerMem 基础层**：

![记忆页头部 + OpenClaw 桥接](./images/01-overview.png)

**OpenClaw 桥接** 卡片展示 ClawMaster 发货的 `memory-clawmaster-powermem` 插件是否已经接管 OpenClaw 的 memory slot。首次进入默认是 **需要同步**；点右上角 **同步桥接**：

- 插件被 link 到 OpenClaw（`openclaw plugins install -l <path>`）
- `plugins.slots.memory` 切到 `memory-clawmaster-powermem`
- `plugins.entries.memory-clawmaster-powermem` 写入当前 profile 的 dataRoot

同步完成后就会显示上图的 **桥接已就绪** + 「插件已安装」+ `当前 slot: memory-clawmaster-powermem`，路径全部指向 `~/.clawmaster/data/default/memory/powermem/`。之后新的 `memory_add` 写进这里，不再回写 workspace markdown。

> ⚠️ 如果状态一直停在 **当前不支持**：你在 web-only 模式里（桌面/Tauri 模式才有托管 runtime）。

---

## 第 2 步：一键导入旧的 workspace 记忆

桥接就绪后，滚到 **旧版记忆导入** 区块，点 **导入 OpenClaw 记忆**：

![导入完成，4/4/4 + 六项指标](./images/02-import-after.png)

后端扫描 `~/.openclaw/workspace/MEMORY.md` + `memory/**/*.md`，按 `(agentId, path, content)` 算 SHA-256 指纹存成 PowerMem 记录。第二次点按钮六项会全在 **跳过**（幂等），文件改了会 **更新**，文件删了会级联从 PowerMem 清掉对应记录。

指纹记在 `~/.clawmaster/data/<profile>/memory/powermem/openclaw-import-state.json`。

---

## 第 3 步：看三张证据卡

往下翻到 **为什么 PowerMem 更好用**：

![三张证据卡：4/4 覆盖 · 1 条直接保存 · 旧版 CLI 不可用](./images/03-proof-cards.png)

- **旧记忆可直接带过来**（琥珀色）：`4 / 4` — workspace 4 份 markdown 全部落进 PowerMem
- **新事实直接保存**（翠绿色）：稍后 UI 写入后会从 `0` → `1` → …，永远不回写文件
- **召回效果一眼看清**（天蓝色）：新版 OpenClaw CLI（2026.4.15+）已经下线了 `openclaw memory search` 子命令，所以并排对比暂时不可用。反过来说：**CLI 搜索都不维护了，UI 里的语义召回就是唯一通路**——也正是这个任务存在的理由。

---

## 第 4 步：写一条新事实，跑一次语义搜索

滚到最下方的写入/搜索区，在大框里敲一条跟 workspace 无关的新事实，点 **写入记忆**。我这次写的是：

> 深圳站工作坊的场地是 T 空间 B2 层 801，需要提前 30 分钟开门布置投影和延长线。

然后在搜索框敲 **「下一场工作坊在哪里」** → **搜索**：

![语义搜索：workspace 导入 + UI 写入一起命中](./images/04-search-hit.png)

返回的 5 条按相关性排序，每条有 `score` + `agent: main`：

- `0.187` — `user_role.md`（「Haili，常驻深圳 ... 线下工作坊的组织者」）← 来自 workspace 导入
- `0.144` — `MEMORY.md` 索引
- `0.083` — `workshop_goal.md`（「深圳 日期 2026-04-25」）← workspace 导入
- `0.060` — `writing_style.md` ← workspace 导入
- `0.021` — 「深圳站工作坊的场地是 T 空间 B2 层 801 ...」← **UI 刚写入的新事实**

workspace 导入和 UI 直接写入在同一个语义搜索里并列出现，不需要分开管理。

**最近托管记忆** 会把来源标出来：

![最近列表：从记忆页写入 vs 导入的 workspace](./images/05-recent-mixed.png)

- 🟠 **从记忆页写入** — UI 直接写的事实
- ⚪ **导入的 workspace** + `agent: main` — 来自 `~/.openclaw/workspace/memory/*.md`

---

## 第 5 步：让 OpenClaw agent 跨会话召回这些记忆

启动 gateway（如果还没起）：

```bash
openclaw gateway run --bind loopback --auth none &
```

在**全新的 shell 里**跑 `openclaw agent` 问它跟这些记忆相关的问题：

```bash
$ openclaw agent -m "我下一场工作坊地点在哪里？" --agent main --local
[plugins] memory-clawmaster-powermem: auto-captured 1 memory chunk(s)

根据你的长期记忆，下一场线下工作坊的地点是 **深圳 T 空间 B2 层 801**，
日期是 **2026-04-25**。

需要提前 30 分钟到场布置投影和延长线。
```

注意答案把 **workspace 导入**（`workshop_goal.md` 里的「深圳 / 2026-04-25」）和 **UI 直接写入**（「T 空间 B2 层 801 / 30 分钟 / 投影延长线」）**揉在了一起**——这是 agent 通过 `memory_recall` 工具从 PowerMem 语义检索出多条记忆后合成的回答。gateway 日志里看得到：

```
[plugins] memory-clawmaster-powermem: auto-captured 1 memory chunk(s)
```

再问一条风格类的问题，验证 workspace 记忆确实活在这个 agent 的上下文里：

```bash
$ openclaw agent -m "我偏好的写作风格是什么？别用'新特性'当亮点，先给结论还是先给数据？" --agent main --local

根据你的长期记忆，你的写作风格偏好如下：
1. **先给结论再给数据**：直接点明核心观点，再用数据或细节支撑。
2. **避免用"新特性"作为亮点**：更注重实际价值和工程实证。
3. **中文技术社区语气**：自然、简洁，避免营销话术。
```

每条答案都直接对应 `writing_style.md` 里的 frontmatter 内容。

回到 ClawMaster 的 **会话** 页，这条 `agent:main:main` 会话就挂在列表里，token 用量和最近更新时间随 agent 调用实时增长：

![会话页：活跃会话消耗了 17K token 去读 PowerMem](./images/06-session-active.png)

会话是 **直连** 模式，模型 DeepSeek-V3 / siliconflow，17.2K in / 159 out——大部分 input token 就是 agent 从 PowerMem 拉出来喂给模型的记忆上下文。

---

## 验证

```bash
# 桥接指向随包插件
jq '.plugins.slots, .plugins.installs["memory-clawmaster-powermem"] | .source' \
  ~/.openclaw/openclaw.json
# { "memory": "memory-clawmaster-powermem" }
# "path"

# 导入指纹 — 每个 markdown 按 (agentId, path, content) 一行
jq '.lastRun, (.sources | length)' \
  ~/.clawmaster/data/default/memory/powermem/openclaw-import-state.json

# PowerMem SQLite 条目数（应该 = 旧 markdown 数 + UI 写入数）
sqlite3 ~/.clawmaster/data/default/memory/powermem/powermem.sqlite \
  "SELECT COUNT(*) FROM memories;"

# 托管 stats API 快速看
curl -sS http://localhost:16224/api/memory/managed/stats \
  | jq '{totalMemories, userCount, engine}'

# 直接跑语义搜索
curl -sS -X POST http://localhost:16224/api/memory/managed/search \
  -H 'Content-Type: application/json' \
  -d '{"query":"下一场工作坊在哪里"}' | jq '.[0:3] | .[] | {score, content: .content[0:80]}'
```

---

## 常见问题

**Q：点「同步桥接」后报错 `installation blocked: dangerous code patterns`** → 这是 OpenClaw 的插件安全扫描拦了随包插件（它用 `child_process` 调 `openclaw memory status --json`）。解决方案：在 shell 里先手动装一次，带上 `--dangerously-force-unsafe-install` 绕过：
```bash
openclaw plugins install --link --dangerously-force-unsafe-install \
  $(npm root -g)/clawmaster/plugins/memory-clawmaster-powermem
```
装完再回 UI 点一次同步桥接即可。

**Q：同步一直报 `drifted`** → **待处理问题** 列表会指明哪个字段漂移。多数是 `dataRoot` 被别的 profile 改过；把 ClawMaster 设置里的 profile 切过去再同步。

**Q：导入 `failed > 0`** → 打开 `openclaw-import-state.json`，对照 `sources` 找没拿到 `memoryId` 的条目。最常见是空文件或只有 frontmatter 没正文。

**Q：我又在 Claude Code / OpenClaw 里写了新 MEMORY** → 桥接不监听文件系统，回来点一次 **导入 OpenClaw 记忆** 即可。指纹没变的 skipped，内容变了 updated。

**Q：agent 答不上来记忆里的问题** → 检查 gateway 是不是起在同一个 profile（`openclaw --profile default gateway run`）。不同 profile 有各自的 PowerMem data root。

**Q：召回对比栏灰着** → 新版 OpenClaw CLI 去掉了 `openclaw memory search` 子命令，所以对比不了。单独的 PowerMem 语义搜索仍然正常。

---

## 下一步

- Build：让 `content-draft` 的 `memory_recall` 命中你刚迁移的"写作偏好"，生成的草稿自动按风格套板
- Observe：看看 agent 会话的 token 消耗怎么随 `memory_recall` 命中增长 → [cron-cost-digest](../../observe/cron-cost-digest/README_CN.md)
- Save 续作：给不同的 agent / user id 分区存记忆，观察 PowerMem 按 scope 隔离召回结果
