# 审计缓冲（所有 Agent 可写）

**用途**: Worker Agent 记录自己的关键操作，供 Main Agent 汇总
**写入权限**: 所有 Agent 可追加
**消费方式**: Main Agent **每周自动**读取并归档（通过 Cron 任务）
**清理**: Main Agent 自动清空（无需手动处理）

---

## 记录规范

### 谁需要记录
所有 Worker Agent

### 何时记录
| 操作 | 示例 |
|------|------|
| Cron 任务创建 | 创建了新定时任务 |
| Cron 任务修改 | 修改了任务时间、内容等 |
| Cron 任务删除 | 删除了定时任务 |
| 跨 Agent 访问 | 访问了其他 Agent 的 shared/ 内容 |

### 格式
```
[YYYY-MM-DD HH:MM] [AGENT_LABEL] [ACTION] 详情描述
```

### 示例
```
[2026-03-18 14:30] [{label1}] [CRON_CREATE] 任务: {label1}-晚报任务, schedule: 0 18 * * *
[2026-03-18 15:00] [{label2}] [CRON_UPDATE] 任务: {label2}-视频发布, 变更: 时间改为 20:00
[2026-03-18 16:00] [{label3}] [ACCESS] 读取: shared/knowledge/企业知识库.md
```

---

## 日志条目

