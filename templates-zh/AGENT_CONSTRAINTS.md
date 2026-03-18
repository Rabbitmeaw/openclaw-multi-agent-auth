# Agent 权限约束

**适用对象**: 所有 Worker Agent
**生效日期**: 2026-03-18

---

## 核心规则

### 1. Cron 任务自治

#### ✅ 允许的操作
- 创建定时任务（必须带 `{label}-` 前缀）
- 修改自己的任务（以 `{label}-` 开头）
- 删除自己的任务
- 查看所有任务（用于自检）

#### ❌ 禁止的操作
- 创建无前缀的任务
- 使用他人的前缀
- 修改/删除他人的任务

#### 命名规范
```
格式: {label}-{任务名}
示例: {label1}-早报, {label2}-视频发布

如何确定你的 label：
读取 ~/.openclaw/shared/AGENT_REGISTRY.md
```

### 2. 审计上报（强制）

执行以下操作时，**必须**追加到 `~/.openclaw/shared/AUDIT_BUFFER.md`：

| 操作 | 格式 |
|------|------|
| 创建任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_CREATE] 任务名: xxx, schedule: yyy` |
| 修改任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_UPDATE] 任务名: xxx, 变更: yyy` |
| 删除任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_DELETE] 任务名: xxx` |

### 3. 文件系统边界

#### ✅ 可访问
- `workspace-{label}/` — 你的私有区
- `shared/` — 共享区（按子目录权限）
- `shared/AUDIT_BUFFER.md` — 审计上报

#### ❌ 禁止访问
- `workspace/` — Main Agent 私有区
- 其他 Agent 的 workspace

---

## 执行流程

### 创建 Cron 任务的正确步骤

```
1. 确认身份
   → 读取 ~/.openclaw/shared/AGENT_REGISTRY.md
   → 确定你的 label

2. 创建任务
   → cron add --name "{label}-任务名" ...
   → 确保名称包含 {label}- 前缀

3. 记录审计
   → 追加到 ~/.openclaw/shared/AUDIT_BUFFER.md
   → 格式：[时间] [label] [CRON_CREATE] 详情

4. 验证
   → cron list | grep "{label}-"
   → 确认任务创建成功
```

### 修改/删除任务的正确步骤

```
1. 列出你的任务
   → cron list | grep "{label}-"

2. 确认目标任务是你要操作的
   → 检查任务名是否以 {label}- 开头

3. 执行修改/删除
   → cron update --jobId xxx ...
   → 或 cron remove --jobId xxx

4. 记录审计
   → 追加到 AUDIT_BUFFER.md
```

---

## 冲突处理

如果发现以下情况，**立即停止操作并联系 Main Agent**：
- 你想创建的任务名已被他人使用
- 你不确定某个任务是否属于你
- 你误删了他人的任务
- 你发现系统异常

---

## 与 Main Agent 的关系

| 场景 | 你的做法 | Main Agent 的角色 |
|------|----------|------------------|
| 日常 Cron 管理 | 自主创建/修改/删除 | 不干预 |
| 命名冲突 | 停止操作，联系 Main | 仲裁协调 |
| 需要帮助 | 通过 shared/communication/ 或飞书联系 | 提供支持 |
| 系统维护 | 配合 Main 的要求 | 统一调度 |

---

## 关键文件

| 文件 | 路径 | 用途 |
|------|------|------|
| 本文件 | `~/.openclaw/shared/AGENT_CONSTRAINTS.md` | 权限约束 |
| 注册表 | `~/.openclaw/shared/AGENT_REGISTRY.md` | 确认身份 |
| 审计缓冲 | `~/.openclaw/shared/AUDIT_BUFFER.md` | 上报操作 |
| 审计日志 | `~/.openclaw/workspace/AUDIT_LOG.md` | 正式审计记录（Main only） |
| 治理规范 | `~/.openclaw/shared/GOVERNANCE.md` | 企业准则 |
| 维护指南 | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | Main Agent 维护手册 |
| 季度归档 | `~/.openclaw/archive/YYYY-Qx/` | 历史审计数据归档 |
