# promo-planner

开源项目推广方案规划 + Claude Code /schedule 定时提醒执行。

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
| 设置提醒 | `/schedule` 定时推送 | 🤖 AI |
| 发布 | 打开 App 粘贴 | 👤 你 |
| 复盘 | 记录数据，调整策略 | 👤+🤖 |

## 搭配技能

```
promo-planner → social-media-cn → /schedule
   ↓                ↓                ↓
方案+日历      每篇文案         定时提醒
```

## 目录

```
promo-planner/
├── SKILL.md                      # 技能定义
├── README.md
├── references/
│   └── schedule-patterns.md      # Cron 表达式速查
├── promo-plans/                  # 推广方案
│   ├── xxx-plan.md
│   └── xxx-calendar.md
└── execution-log/                # 执行记录
    └── xxx/
        └── date-platform.md
```
