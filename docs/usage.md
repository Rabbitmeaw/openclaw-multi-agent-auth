# 使用指南

## 5.1 Worker Agent 操作指南

### 创建 Cron 任务

```javascript
// 步骤 1: 确认身份
const registry = read("~/.openclaw/shared/AGENT_REGISTRY.md");
const myLabel = "{label}";  // 你的 label

// 步骤 2: 创建任务（必须带前缀）
cron add({
  name: "{label}-日报提醒",        // ✅ 正确：带前缀
  schedule: { kind: "cron", expr: "0 9 * * *" },
  payload: {
    kind: "agentTurn",
    message: "执行日报生成..."
  },
  delivery: {
    mode: "announce",
    channel: "feishu",
    to: "用户open_id"
  }
});

// 步骤 3: 记录审计（必须）
appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
  "[2026-03-18 14:30] [{label}] [CRON_CREATE] 任务名: {label}-日报提醒, schedule: 0 9 * * *\n");

// 步骤 4: 验证创建
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));
```

### 修改 Cron 任务

```javascript
// 步骤 1: 列出自己的任务
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));

// 步骤 2: 确认任务属于自己
const targetJob = myJobs.find(j => j.id === targetJobId);
if (!targetJob) {
  // ❌ 禁止操作他人任务
  notify("错误：只能操作自己的任务");
  return;
}

// 步骤 3: 执行修改
cron.update({
  jobId: targetJobId,
  patch: { schedule: { expr: "0 10 * * *" } }
});

// 步骤 4: 记录审计
appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
  "[2026-03-18 15:00] [{label}] [CRON_UPDATE] 任务名: {label}-xxx, 变更: schedule调整\n");
```

### 删除 Cron 任务

```javascript
// 只能删除自己的任务
const myJobs = cron.list().filter(j => j.name.startsWith("{label}-"));
if (myJobs.some(j => j.id === targetJobId)) {
  cron.remove({ jobId: targetJobId });

  // 记录审计
  appendToFile("~/.openclaw/shared/AUDIT_BUFFER.md",
    "[2026-03-18 16:00] [{label}] [CRON_DELETE] 任务名: {label}-xxx\n");
}
```

---

## 5.2 Main Agent 操作指南

### 监督全局任务

```javascript
// 查看所有任务
const allJobs = cron.list();

// 检查命名规范
const violations = allJobs.filter(j => {
  const validPrefixes = ["main-", "{label1}-", "{label2}-", ...];
  return !validPrefixes.some(p => j.name.startsWith(p));
});

if (violations.length > 0) {
  notify(`发现 ${violations.length} 个违反命名规范的任务`);
  // 通知相关 Agent 整改
}
```

### 仲裁冲突

```javascript
// 发现前缀冲突时的处理流程
function resolveConflict(agentA, agentB, prefix) {
  // 1. 联系双方 Agent
  sessions_send({ label: agentA, message: "前缀冲突通知..." });
  sessions_send({ label: agentB, message: "前缀冲突通知..." });

  // 2. 协商解决方案
  // - 方案1: 一方更换前缀
  // - 方案2: 合并任务
  // - 方案3: 强制重命名（紧急情况下）

  // 3. 执行解决方案
  cron.update({ jobId: xxx, patch: { name: "new-prefix-task" } });

  // 4. 记录审计
  appendToFile("~/.openclaw/workspace/AUDIT_LOG.md",
    "[时间] [main] [ARBITRATION] 冲突解决: agentA vs agentB, 方案: xxx");
}
```

### 审计维护（每周日 9:00 自动执行）

详见 `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md`

```javascript
// 消费缓冲日志
const buffer = read("~/.openclaw/shared/AUDIT_BUFFER.md");
// 提取条目 → 追加到 AUDIT_LOG.md → 清空缓冲

// Cron 变更巡检
const currentJobs = cron.list();
const lastSnapshot = readJson("~/.openclaw/workspace/.cron_snapshot.json");
// 对比差异 → 记录变更 → 保存新快照

// 季度归档检查
if (isQuarterStart()) {
  // 归档本季度数据 → 创建新季度目录
}
```

---

## 5.3 用户查询操作

### 查看所有任务

```bash
openclaw cron list
```

### 按前缀筛选任务

```bash
# 查看 {label} 的所有任务
openclaw cron list | grep "{label}-"
```

### 查看审计日志（仅 Main）

```bash
cat ~/.openclaw/workspace/AUDIT_LOG.md
```

### 查看 Agent 注册表

```bash
cat ~/.openclaw/shared/AGENT_REGISTRY.md
```

---

## 5.4 常见问题

### Q: Worker Agent 如何知道该用什么前缀？

A: 读取 `~/.openclaw/shared/AGENT_REGISTRY.md`，查找自己的 label 对应的 Cron 前缀。

### Q: 如果不小心创建了不带前缀的任务怎么办？

A: Main 会定期巡检发现，联系该 Agent 要求重命名或清理。

### Q: Worker 可以修改其他 Agent 的任务吗？

A: 技术上可行（cron 工具没有硬限制），但按规则禁止。违反者由 Main 仲裁。

### Q: Main 可以访问 Worker 的 workspace 吗？

A: 技术上可以，但按规则仅在明确授权、系统维护、调试支持、紧急干预时访问。

### Q: 如何添加新的 Agent？

1. 创建新的 workspace: `workspace-{label}/`
2. 在 `AGENT_REGISTRY.md` 注册
3. 分配 Cron 前缀
4. 创建 SOUL.md（包含权限约束）

---

## 5.5 故障排查

| 问题 | 检查 | 解决 |
|------|------|------|
| 任务命名冲突 | `AGENT_REGISTRY.md` | 联系 Main 协调前缀 |
| 误删他人任务 | `cron list` 历史 | 从备份恢复或重新创建 |
| 任务不执行 | `cron status` | 检查 Gateway 状态 |
| 权限争议 | `AGENT_REGISTRY.md` | Main 仲裁 |
| 审计不记录 | `AUDIT_BUFFER.md` 权限 | 检查文件是否可写 |

---

## 5.6 最佳实践

### 命名规范

- 使用有意义的任务名：`{label}-功能-描述`
- 示例：`work-日报-早间推送`、`study-英语-每日单词`

### 审计记录

- 及时记录，不要事后补录
- 记录要包含足够上下文（时间、操作、详情）
- 定期检查审计日志完整性

### 协作沟通

- 优先使用 `shared/communication/`
- 紧急情况可直接联系 Main
- 保持沟通记录，便于追溯

### 定期维护

- Main Agent 每周执行审计维护
- 每季度审查归档数据
- 及时清理过期数据（超 4 季度）
