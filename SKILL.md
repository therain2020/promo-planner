---
name: promo-planner
description: 开源项目推广方案规划与定时执行。自动分析项目代码提炼推广角度，生成多平台内容日历，通过 Claude Code CronCreate 设置定时提醒，追踪执行进度。Use when user asks to plan promotion for an open-source project, create a content calendar, schedule social media posts, or track marketing execution.
---

# 推广方案规划 + 定时执行 · Promo Planner

**你只做一件事：收到提醒 → 打开 App → 粘贴文案 → 点发布。其余全自动。**

---

## 触发条件

当用户说以下任意一句时，自动执行本 skill：

- "给我的项目做推广方案"
- "生成推广计划"
- "排一下发布日历"
- "/promo-planner [项目路径]"

---

## 依赖技能

本 skill 依赖以下技能，请确保已安装：

| 技能 | 用途 | 安装命令 |
|------|------|---------|
| social-media-cn | 按方案生成各平台文案 | `npx skills add therain2020/social-media-cn -g -y` |
| humanizer-zh | 去除文案 AI 生成痕迹 | `npx skills add therain2020/humanizer-zh -g -y` |

可用 `Skill(skill="social-media-cn")` 和 `Skill(skill="humanizer-zh")` 调用。

---

## 执行流程

skill 被调用后，严格按以下 6 步执行。每完成一步，输出进度标记。

### Step 0：项目分析（子 Agent，必须）

派出 Explore 子 Agent 读取项目代码：

```
task(
  subagent_type="Explore",
  description="分析项目提炼推广信息",
  prompt="
  全面探索 [PROJECT_PATH]，提炼以下信息并保存到 [PROJECT_PATH]/promo-analysis.md：

  ## 1. 一句话价值主张
  （1 句话，中文，普通人听得懂）

  ## 2. 核心痛点（3条）
  每条格式：用户 [谁] 在 [场景] 遇到 [问题]，结果是 [后果]

  ## 3. 目标用户画像（3个）
  每个格式：**[标签]** — 1句话描述 + 他们会在哪出没

  ## 4. 功能亮点（按wow-factor排序 1-5分）
  | 功能 | 一句话 | Wow分 | 适合截图吗 |
  |------|--------|-------|-----------|

  ## 5. 技术栈亮点
  列出3-5个最值得对外说的技术选择，每个配1句"为什么选它"

  ## 6. 上手指南
  - 安装步骤数：[N]步
  - 上手时间：[N]分钟
  - 最难的一步是：
  - 最容易劝退的一步是：

  ## 7. 竞品对比（如果有）
  | 对比维度 | 本项目 | 替代方案A | 替代方案B |
  （填你知道的，不确定的标\"[待验证]\"）

  ## 8. 最佳截图时刻（5个）
  列出最适合做配图/封面的操作瞬间，描述屏幕上能看到什么
  "
)
```

Agent 完成后，读取 `promo-analysis.md`，确认内容完整。

### Step 1：生成推广方案 + 提纲（必须产出两个文件）

基于 `promo-analysis.md`，生成以下文件。

#### 文件 1：`promo-plans/[project]-plan.md`

```markdown
# [项目名] 推广方案

> 生成时间：[ISO时间]
> 基于分析：[promo-analysis.md]

## 基本信息
- 项目：[链接]
- 价值主张：[1句话]
- 目标受众：[3个标签]
- 推广周期：4周

## 推广目标
| 平台 | 篇数 | 目标互动/篇 | 目标Star |
|------|------|------------|---------|
| 小红书 | 6 | 50+ | — |
| 小黑盒 | 4 | 20+ | — |
| GitHub | — | — | +20 |

## 6 角度 × 具体 Hook

每个角度写一句可发布的标题 Hook：

| # | 角度 | 平台 | Hook（直接可用） |
|---|------|------|-----------------|
| A | 痛点 | 小红书 | [基于分析 #2 写] |
| B | 技术 | 小黑盒 | [基于分析 #5 写] |
| C | 对比 | 小红书 | [基于分析 #7 写] |
| D | 开源 | 小红书 | [基于分析 #1 写] |
| E | 踩坑 | 小黑盒 | [基于分析 #6 写] |
| F | 教程 | 全平台 | [基于分析 #4 写] |

## 内容矩阵（4周）

| 周 | 日期 | 平台 | 角度 | Hook | 内容文件 | 状态 |
|----|------|------|------|------|---------|------|
| W1 | [周一] | 小红书 | A | [Hook A] | [链接] | ⬜ |
| W1 | [周三] | 小黑盒 | B | [Hook B] | [链接] | ⬜ |
| W1 | [周五] | 小红书 | D | [Hook D] | [链接] | ⬜ |
| W2 | [周一] | 小黑盒 | E | [Hook E] | [链接] | ⬜ |
| W2 | [周四] | 小红书 | F | [Hook F] | [链接] | ⬜ |
| W2 | [周六] | 小黑盒 | B变体 | [变体Hook] | [链接] | ⬜ |
| W3 | [周二] | 小红书 | C | [Hook C] | [链接] | ⬜ |
| W3 | [周五] | 小黑盒 | E变体 | [变体Hook] | [链接] | ⬜ |
| W4 | [周三] | 小红书 | A变体 | [变体Hook] | [链接] | ⬜ |
| W4 | [周六] | 小黑盒 | 总结 | [汇总Hook] | [链接] | ⬜ |

## 素材清单
- [ ] 封面图（Canva模板：标题大字 + 项目名）
- [ ] 功能截图 × 3（见分析 #8）
- [ ] 安装流程截图 × 3（见分析 #6）
- [ ] 效果对比图 × 2（用前 vs 用后）
```

#### 文件 2：`promo-plans/[project]-schedule.md`

记录所有已创建的 `CronCreate` 提醒，格式：

```markdown
# 提醒清单

| ID | 时间 | Cron | 内容 | 状态 |
|----|------|------|------|------|
| 1 | W1-D1 12:28 | 28 12 [日] [月] * | 小红书发布：... | active |
| 2 | W1-D1 前一天 | 0 20 [日-1] [月] * | 准备文案：... | active |
| ... | ... | ... | ... | ... |
```

### Step 2：调度文案生成（调用 social-media-cn）

按内容矩阵从上到下，对每个 ⬜ 槽位执行以下序列。

#### 2a. 调用 social-media-cn

使用 Skill 工具调用 social-media-cn，传入结构化参数（协议详见本文"通信协议"节）：

```
Skill(skill="social-media-cn", args="
平台：[小红书/小黑盒]
角度：[A-痛点/B-技术/C-对比/D-开源/E-踩坑/F-教程]
Hook：[内容矩阵中的 Hook 文案]
项目分析路径：[promo-analysis.md 的绝对路径]
输出路径：[promo-content/[project]/[platform]/[date]-[angle].md 的绝对路径]
")
```

social-media-cn 应返回 `✅ [平台] [角度] → [文件路径]`。

#### 2b. 去 AI 味

social-media-cn 生成完成后，调用 humanizer-zh skill 对输出文件去 AI 味：

```
Skill(skill="humanizer-zh", args="[输出文件的绝对路径]")
```

#### 2c. 更新状态

在内容矩阵中将对应槽位状态更新为 ✅，填入文案文件路径。

#### 2d. 依赖缺失处理

如果 social-media-cn skill 未安装（Skill 工具返回错误），不自行生产文案。而是：
1. 输出提示：`⚠️ social-media-cn skill 未安装。请先运行：`
   `npx skills add therain2020/social-media-cn -g -y`
2. 将该槽位状态标记为 `⚠️ 等待安装`
3. 继续处理剩余槽位

**Step 2 输出**：每调度一篇，输出 `✅ [平台] [角度] → [文件路径]` 或 `⚠️ [槽位] 等待 social-media-cn 安装`

### Step 3：创建 CronCreate 定时提醒

对内容矩阵中的每个发布槽位，创建 3 层提醒：

**第 1 层 — 准备提醒（发布前 1 天 20:00）：**
```
CronCreate(
  cron: "3 20 [发布日-1] [调整后的月] *",  // 日-1 跨月时月份也要调整，见规则 6
  prompt: "📋 明天发布预告：小红书《[Hook]》。确认文案和配图已就绪。路径：[文件路径]",
  recurring: false
)
```

**第 2 层 — 发布提醒（发布当天，提前 2 分钟）：**
```
CronCreate(
  cron: "[随机分钟 避开0/30] [小时] [日] [月] *",
  prompt: "🚀 现在发布：小红书《[Hook]》。打开App → 粘贴文案 → 发布。文案路径：[文件路径]",
  recurring: false
)
```

**第 3 层 — 复盘提醒（发布后 24 小时）：**
```
CronCreate(
  cron: "[随机分钟] [小时] [日+1] [调整后的月] *",  // 日+1 跨月时月份也要调整，见规则 6
  prompt: "📊 复盘：[平台]《[Hook]》。打开 execution-log-template 模板记录互动数据到 execution-log/[project]/[日期]-[平台].md。效果好的话考虑做变体。",
  recurring: false
)
```

**Step 3 输出**：每创建一个提醒，输出 `⏰ [层]提醒已设置 → Cron [表达式]`

**重要**：创建 Cron 时避开分钟 0 和 30，用 3/7/13/17/23/27/33/37/43/47/53/57。

### Step 4：打印执行清单

全部设置完成后，输出最终摘要：

```
═══════════════════════════════════════════
  [项目名] 推广方案已就绪
═══════════════════════════════════════════
方案文件：  promo-plans/[project]-plan.md
提醒清单：  promo-plans/[project]-schedule.md
文案目录：  promo-content/[project]/
═══════════════════════════════════════════
内容：      [N] 篇文案已生成
提醒：      [N×3] 个 CronCreate 已创建
首发：      [日期] [平台]《[Hook]》
═══════════════════════════════════════════

接下来你只需：
  收到提醒 → 打开App → 粘贴文案 → 点发布
  24h 后复盘提醒 → 按 Step 5 记录数据

CronList 可查看所有提醒
═══════════════════════════════════════════
```

### Step 5：复盘流程（复盘提醒触发时执行）

当第 3 层复盘 CronCreate 触发时，执行以下操作：

1. 打开平台 App/网页，截图互动数据
2. 按 `references/execution-log-template.md` 模板，创建 `execution-log/[project]/[YYYY-MM-DD]-[平台].md`
3. 填入互动数据（阅读/点赞/收藏/评论/分享），记录评论精选和自我评估
4. 回写 `promo-plans/[project]-plan.md` 内容矩阵对应行状态：
   - 数据 > 预期的 150%（超出预期 50% 以上）→ 标记 🔥，追加一行变体排期（W+1），为新槽位执行 Step 2 + Step 3
   - 数据在预期的 50%~150% 之间 → 标记 ✅
   - 数据 < 预期的 50% → 标记 ⚠️，记录原因，决定是否调整角度
5. 更新 `promo-plans/[project]-schedule.md` 中对应提醒状态为 `completed`

**Step 5 输出**：`📊 [平台]《[Hook]》复盘 → [关键数据] | 决策: [做变体/调整角度/继续观察]`

---

## 执行中规则

1. **日期计算**：从"今天 + 2天"开始排第一个发布日（留时间准备素材），按周递推
2. **避开冲突**：两个平台不排同一天，至少间隔 1 天
3. **小红书优先周一/周四/周五**，**小黑盒优先周三/周六**
4. **Cron 分钟随机化**：用 3,7,13,17,23,27,33,37,43,47,53,57 中的一个，不准用 0 或 30
5. **文案必须先过 humanizer-zh**：本 skill 在 Step 2b 调用 humanizer-zh 对 social-media-cn 产出的文案去 AI 味。social-media-cn 侧不自行调用
6. **日期合法性校验**：计算 Cron 日字段时，如果 `日±N` 超出当月范围，必须进位或退位到相邻月份。例如 1月31日 +1 → 2月1日，3月1日 -1 → 2月28/29日。CronCreate 不会自动处理溢出，传无效日期会静默失败。

---

## 增量模式（后续调用）

如果用户说"继续推广 [项目]"或"加一篇"：

1. 读取 `promo-plans/[project]-plan.md`
2. 检查哪些槽位状态为 ⬜
3. 只对 ⬜ 槽位执行 Step 2 + Step 3
4. 更新状态

---

## 通信协议

> 本段与 social-media-cn SKILL.md 保持同步。promo-planner 与 social-media-cn 的调用协议。

### 角色

| 角色 | 技能 | 职责 |
|------|------|------|
| 方案方 | promo-planner | 项目分析 → 角度提炼 → 内容矩阵 → Hook 标题 → 排期日历 → CronCreate |
| 执行方 | social-media-cn | 接收参数 → 按角度选结构 → 按平台套模板 → 产出文案 → 返回路径 |

### 调用参数（方案方 → 执行方）

方案方调用执行方时传入以下结构化参数：

```
平台：[小红书 | 小黑盒]
角度：[A-痛点 | B-技术 | C-对比 | D-开源 | E-踩坑 | F-教程]
Hook：[已写好的标题文案，执行方直接使用]
项目分析路径：[promo-analysis.md 的绝对路径]
输出路径：[目标文件绝对路径]
```

### 角度 → 正文结构

| 角度 | 叙事结构 |
|------|---------|
| A-痛点 | 痛点场景 → 为什么以前解决不了 → 这个方案怎么解决的 |
| B-技术 | 技术背景 → 方案架构 + 关键实现 → 与替代方案的区别 |
| C-对比 | 主流方案简述 → 逐项对比 → 适用场景建议 |
| D-开源 | 为什么做 → 核心亮点 + 技术栈 → Star/PR 邀请 |
| E-踩坑 | 踩坑背景 → 逐坑(问题→原因→解决) → 经验总结 |
| F-教程 | 目标读者 → 分步教程 → 常见问题 |

### 模式判断（执行方）

执行方通过调用内容是否包含 `平台：` + `角度：` + `项目分析路径：` 来判断：
- **协作模式**（含这三个标记）→ 跳过 Step 0，读取分析文件，用 Hook 做标题
- **独立模式**（不含）→ 完整流程，自己做项目分析

### 输出约定

- 协作模式：保存到方案方指定的 `输出路径`
- 独立模式：保存到 `promo-content/[平台]/[日期]-[主题]/note.md`
- 完成后均输出：`✅ [平台] [角度] → [文件路径]`

### 错误处理

- 执行方不可用：方案方终止 Step 2，提示 `npx skills add therain2020/social-media-cn -g -y`
- 项目分析文件不存在：执行方终止并报错，提示方案方先完成 Step 0

---

## 搭配技能

```
promo-planner → social-media-cn → humanizer-zh → CronCreate
     ↓               ↓               ↓            ↓
 方案+日历      每篇文案        去 AI 味    定时提醒
```

---

## 文件结构

本 skill 只写以下目录。`promo-content/` 由 social-media-cn 产出，本 skill 不写。

```
[project-path]/
├── promo-analysis.md                  # Step 0 产出，供 social-media-cn 读取
│
├── promo-plans/                       # 本 skill 产出
│   ├── [project]-plan.md              # 推广方案 + 内容矩阵
│   └── [project]-schedule.md          # CronCreate 提醒清单
│
├── promo-content/                     # social-media-cn 产出（本 skill 不写此目录）
│   └── [project]/
│       ├── xiaohongshu/
│       │   ├── [date]-[angle].md
│       │   └── ...
│       └── xiaoheihe/
│           └── ...
│
├── execution-log/                     # 发布后复盘记录
│   └── [project]/
│       └── [date]-[platform].md
│
└── references/
    ├── schedule-patterns.md           # Cron 表达式速查
    └── execution-log-template.md      # 复盘记录模板
```
