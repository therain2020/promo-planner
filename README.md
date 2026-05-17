# promo-planner

开源项目推广方案规划 + Claude Code CronCreate 定时提醒执行。

## 安装

```bash
npx skills add therain2020/promo-planner -g -y
```

## 功能

| 步骤 | 做什么 | 谁做 |
|------|--------|------|
| 分析项目 | 读代码提炼推广角度 | 🤖 AI |
| 生成方案 | 多平台内容矩阵 + 日历 | 🤖 AI |
| 写文案 | 配合作 `social-media-cn` 生成 | 🤖 AI |
| 设置提醒 | `CronCreate` 定时推送 | 🤖 AI |
| 发布 | 打开 App 粘贴 | 👤 你 |
| 复盘 | 记录数据，调整策略 | 👤+🤖 |

## 搭配技能

```
promo-planner → social-media-cn → CronCreate
   ↓                ↓                ↓
方案+日历      每篇文案         定时提醒
```

## 目录

```
promo-planner/
├── SKILL.md                      # 技能定义
├── README.md
├── references/
│   ├── schedule-patterns.md      # Cron 表达式速查
│   └── execution-log-template.md # 复盘记录模板
├── promo-plans/                  # 推广方案
│   ├── xxx-plan.md
│   └── xxx-schedule.md
├── promo-content/                # 生成的文案
│   └── xxx/
│       ├── xiaohongshu/
│       └── xiaoheihe/
└── execution-log/                # 发布后数据复盘
    └── xxx/
        └── YYYY-MM-DD-platform.md
```
