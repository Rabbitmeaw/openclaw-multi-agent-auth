# 个人 3 Agent 配置示例

## 场景描述

个人用户，使用 3 个 Agent 分别处理不同领域：
- **Main**: 协调者，拥有全局视图
- **Work**: 工作助手，处理职业相关任务
- **Life**: 生活助手，处理日常提醒和计划

## 目录结构

```
~/.openclaw/
├── workspace/                  # Main Agent
├── workspace-work/             # Work Agent
├── workspace-life/             # Life Agent
└── shared/                     # 共享区
```

## Agent 注册表配置

```markdown
| Label | 名称 | Cron 前缀 | 工作区路径 | 权限 |
|-------|------|-----------|------------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 |
| work | Work Agent | `work-` | `workspace-work/` | 自治 |
| life | Life Agent | `life-` | `workspace-life/` | 自治 |
```

## 典型 Cron 任务

### Work Agent
- `work-日报提醒` - 每天 9:00 提醒写日报
- `work-周报生成` - 每周五 17:00 生成周报草稿
- `work-会议提醒` - 根据日历提前提醒

### Life Agent
- `life-喝水提醒` - 每 2 小时提醒喝水
- `life-运动计划` - 每周一、三、五 18:00 提醒运动
- `life-账单提醒` - 每月 1 日提醒查看账单

## 审计配置

- **频率**: 每月检查一次（个人使用可降低频率）
- **归档**: 保留最近 2 季度
- **报告**: 发送到自己的飞书/邮箱

## 设置步骤

1. 按照主文档 [setup.md](../../docs/setup.md) 执行基础设置
2. 修改 `AGENT_REGISTRY.md`，使用上述 label 配置
3. 为 work 和 life 分别创建工作区和 SOUL.md
4. 创建审计维护任务
5. 测试每个 Agent 的 Cron 创建功能
