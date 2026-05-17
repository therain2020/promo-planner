---
name: promo-planner
description: 开源项目推广方案规划与定时执行。自动分析项目代码提炼推广角度，生成多平台内容日历，通过 Claude Code /schedule 设置定时提醒，追踪执行进度。Use when user asks to plan promotion for an open-source project, create a content calendar, schedule social media posts, or track marketing execution.
---

# 推广方案规划 + 定时执行 · Promo Planner

从项目代码到推广执行，全流程自动化。

## 核心理念

AI 帮你做好所有脑力劳动（分析、策划、写稿、排期），你只做 AI 做不了的事（在 App 上点发布按钮）。

## 工作流程

```
分析项目 → 提炼角度 → 生成方案 → 排期日历 → 设置提醒 → 追踪执行
```

---

## Step 0：项目分析与角度提炼（必须）

### 0.1 派出子 Agent 分析项目

```
task(
  subagent_type="Explore",
  description="Analyze project for promotion planning",
  prompt="Thoroughly explore [PROJECT_PATH]. Extract:
  1. Elevator pitch (1 sentence)
  2. Target audience (who needs this? 3-5 personas)
  3. Key features ranked by wow-factor (1-5 scale)
  4. Tech stack highlights
  5. Setup difficulty (1-5)
  6. Competitive advantage (what makes this unique?)
  7. Best screenshot/recording moments
  Report in Chinese, concrete and specific. Save to research/[project]-analysis.md"
)
```

### 0.2 提炼 6 大推广角度

基于分析结果，为项目提炼 6 种叙事角度：

| # | 角度类型 | 适用平台 | 示例方向 |
|---|---------|---------|---------|
| A | 痛点解决 | 全平台 | "被 [痛点] 折磨了 [N] 次后，我做了这个" |
| B | 技术揭秘 | 小黑盒 | "[技术栈] 实现 [功能] 的底层原理" |
| C | 效率对比 | 小红书 | "用之前 vs 用之后，效率差 [N] 倍" |
| D | 开源故事 | 小红书 | "我在 GitHub 开源了一个 [描述] 的项目" |
| E | 踩坑合集 | 小黑盒 | "配置 [功能] 踩了 [N] 个坑，全记录" |
| F | 教程指南 | 全平台 | "[N] 步搞定 [目标]，保姆级教程" |

每个角度写 1 句话的 hook（钩子），中文，有吸引力但不标题党。

---

## Step 1：推广方案生成

### 1.1 输出方案文件

生成 `promo-plans/[project]-[date]-plan.md`，包含：

```markdown
# [项目名] 推广方案

## 基本信息
- 项目：[GitHub 链接]
- 一句话：[价值主张]
- 目标受众：[3 个具体人群]
- 推广周期：[N] 周

## 推广目标
- 小红书：[N] 篇笔记，目标 [N] 互动
- 小黑盒：[N] 篇帖子，目标 [N] 讨论
- GitHub：[N] 个 Star

## 内容矩阵

| 周次 | 平台 | 角度 | 标题 Hook | 状态 |
|------|------|------|-----------|------|
| W1-D1 | 小红书 | A | [hook] | ⬜ |
| W1-D3 | 小黑盒 | B | [hook] | ⬜ |
| W1-D5 | 小红书 | F | [hook] | ⬜ |
| W2-D1 | 小黑盒 | E | [hook] | ⬜ |
| ... | ... | ... | ... | ... |

## 配图/素材清单
- [ ] 项目 Logo/封面图
- [ ] 核心功能截图 x3
- [ ] 安装过程截图 x3
- [ ] 效果对比图 x2
```

### 1.2 推广节奏建议

| 阶段 | 时长 | 内容 | 频率 |
|------|------|------|------|
| 🔥 首发 | 第 1 周 | 痛点 + 开源推荐 | 隔天 1 篇 |
| 📖 深度 | 第 2 周 | 教程 + 技术揭秘 | 每周 2 篇 |
| 💬 互动 | 第 3 周 | 踩坑 + 问答 | 每周 2 篇 |
| 🔄 持续 | 第 4 周+ | 更新动态 + 用户案例 | 每周 1 篇 |

---

## Step 2：内容日历 + /schedule 设置（核心）

### 2.1 生成具体到小时的发布日历

```
promo-plans/[project]-[date]-calendar.md
```

格式示例：

```markdown
# 内容日历

## 2026-05-19 (周一)
- ⏰ 12:30 → 📕 小红书：发布"痛点解决型"笔记
  - 标题：[hook]
  - 状态：⬜
  - 文案：[链接到 note.md]

## 2026-05-21 (周三)  
- ⏰ 20:00 → 🎮 小黑盒：发布"技术揭秘"帖子
  - 标题：[hook]
  - 状态：⬜
  - 文案：[链接到 post.md]
```

### 2.2 用 /schedule 设置定时提醒

为日历中的每个发布节点，创建 `/schedule` 定时任务。

**发布日前一天提醒准备内容：**
```
/schedule "明天发布小红书笔记：[标题]。确认文案和配图已就绪"
```

**发布当天 10 分钟前提醒：**
```
/schedule "10分钟后在小红书发布：[标题]。打开App准备粘贴"
```

**发布后 24 小时复盘提醒：**
```
/schedule "检查昨天小红书笔记的数据：[标题]。记录互动数，决定是否调整策略"
```

### 2.3 提示词模板

对每个排期节点执行：

```
# 设置提醒
CronCreate(
  cron: "[cron表达式，如 28 12 * * 1]",
  prompt: "[提醒内容]",
  recurring: false
)
```

**发布日历 → Cron 表达式转换规则：**
- 周一 12:30 → `28 12 * * 1`
- 周三 20:00 → `58 19 * * 3`（提前 2 分钟）
- 周五 21:00 → `58 20 * * 5`

参考：`references/schedule-patterns.md`

---

## Step 3：执行追踪

### 3.1 发布后记录

```
execution-log/[project]/[YYYY-MM-DD]-[platform].md
```

```markdown
# 发布记录：[平台] - [日期]

- **标题**：[标题]
- **链接**：[帖子链接，发布后手动补填]
- **发布时间**：[实际时间]
- **内容文件**：[文案路径]

## 24h 数据（手动填写）
- 阅读/浏览：[]
- 点赞：[]
- 评论：[]
- 收藏/转发：[]

## 复盘
- 效果评价：⬜ 好 ⬜ 一般 ⬜ 差
- 原因分析：
- 下次改进：
```

### 3.2 周报

每周自动汇总：

```
# 推广周报 —— W[N]

| 平台 | 发布数 | 总互动 | 最高单篇 | 趋势 |
|------|--------|--------|---------|------|
| 小红书 | [N] | [N] | [标题] | ↑/→/↓ |
| 小黑盒 | [N] | [N] | [标题] | ↑/→/↓ |

**下周调整：**
- [基于数据的调整建议]

**Star 增长：**
- 本周：[N] → [N] (+[N])
```

---

## 搭配使用

此 skill 生成方案和日历后，用 `social-media-cn` skill 为每个节点生成具体文案：

```
/social-media-cn → 选择平台 → 选择模板 → 输出文案
```

典型工作流：

```
1. /promo-planner → 分析项目，生成推广方案 + 日历
2. /social-media-cn → 为日历中每个节点生成文案
3. /schedule → 为每个节点设置提醒
4. 收到提醒 → 打开 App → 粘贴文案 → 发布
5. 24h 后 → 记录数据 → 复盘 → 调整策略
```

---

## 文件结构

```
promo-plans/              # 推广方案
├── [project]-plan.md     # 方案主文件
└── [project]-calendar.md # 内容日历
execution-log/            # 执行记录
└── [project]/
    └── [date]-[platform].md
references/
└── schedule-patterns.md  # Cron 表达式参考
```
