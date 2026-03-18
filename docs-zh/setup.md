# 设置方案与执行细节

## 4.1 前置条件

- OpenClaw 网关已安装并运行
- 至少有一个 Main Agent 工作区
- 熟悉 OpenClaw 基本操作

---

## 4.2 快速设置（5分钟）

### 步骤 1：复制模板文件

```bash
cd ~/.openclaw

# 创建共享区目录
mkdir -p shared/communication/{inbox,outbox,tasks}
mkdir -p shared/{skills,knowledge,assets,temp}
mkdir -p archive

# 复制模板文件（使用中文模板）
cp /path/to/this/repo/templates-zh/AGENT_REGISTRY.md shared/
cp /path/to/this/repo/templates-zh/AGENT_CONSTRAINTS.md shared/
cp /path/to/this/repo/templates-zh/GOVERNANCE.md shared/
cp /path/to/this/repo/templates-zh/AUDIT_BUFFER.md shared/
cp /path/to/this/repo/templates-zh/COMPLIANCE_CHECKLIST.md shared/
cp /path/to/this/repo/templates-zh/AUDIT_LOG.md workspace/
cp /path/to/this/repo/templates-zh/AUDIT_MAINTENANCE_GUIDE.md workspace/
```

### 步骤 2：配置 Main Agent

编辑 `~/.openclaw/workspace/SOUL.md`，添加审计责任章节：

```markdown
## 审计与合规责任

### 审计日志维护（自动化）

#### 日志位置
- **正式日志**: `~/.openclaw/workspace/AUDIT_LOG.md`
- **缓冲日志**: `~/.openclaw/shared/AUDIT_BUFFER.md`

#### 维护任务

创建每周日 9:00 的定时任务：

```bash
openclaw cron add \
  --name "main-审计维护" \
  --cron "0 9 * * 0" \
  --session isolated \
  --message "执行审计维护，详见 AUDIT_MAINTENANCE_GUIDE.md" \
  --announce \
  --to "你的open_id"
```

#### 执行内容
1. 消费 AUDIT_BUFFER.md 到 AUDIT_LOG.md
2. Cron 变更巡检（对比快照）
3. 季度归档检查

详细步骤参考 `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md`
```

### 步骤 3：注册 Worker Agent

编辑 `~/.openclaw/shared/AGENT_REGISTRY.md`：

```markdown
| Label | 名称 | Cron 前缀 | 工作区路径 | 权限 |
|-------|------|-----------|------------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 |
| work | Work Agent | `work-` | `workspace-work/` | 自治 |
| study | Study Agent | `study-` | `workspace-study/` | 自治 |
```

### 步骤 4：创建 Worker Agent 工作区

```bash
# 为每个 Worker Agent 创建工作区
mkdir -p ~/.openclaw/workspace-work
mkdir -p ~/.openclaw/workspace-study

# 复制并修改 SOUL.md
cp templates-zh/SOUL_WORKER.md ~/.openclaw/workspace-work/SOUL.md
cp templates-zh/SOUL_WORKER.md ~/.openclaw/workspace-study/SOUL.md

# 修改 SOUL.md 中的 {label} 为实际值（work, study 等）
```

### 步骤 5：初始化审计系统

```bash
# 创建初始快照
openclaw cron list > ~/.openclaw/workspace/.cron_snapshot.json

# 记录初始化日志
echo "## 审计记录 - $(date +%Y-%m-%d) (初始化)" >> ~/.openclaw/workspace/AUDIT_LOG.md
echo "" >> ~/.openclaw/workspace/AUDIT_LOG.md
echo "系统初始化完成" >> ~/.openclaw/workspace/AUDIT_LOG.md
```

### 步骤 6：重启 Gateway

```bash
openclaw gateway restart
```

---

## 4.3 详细设置（完整版）

### 4.3.1 创建物理隔离结构

```bash
# 进入 OpenClaw 配置目录
cd ~/.openclaw

# 1. 确保 Main workspace 存在
ls -la workspace/

# 2. 创建 Worker workspaces
mkdir -p workspace-{label1} workspace-{label2} ...

# 3. 创建 agents 元数据目录
mkdir -p agents/{main,{label1},{label2},...}

# 4. 创建共享区
mkdir -p shared/{communication/{inbox,outbox,tasks},skills,knowledge,assets,temp}

# 5. 创建审计归档区
mkdir -p archive

# 6. 验证结构
tree -L 2
```

### 4.3.2 配置 Agent 注册表

参考 `templates-zh/AGENT_REGISTRY.md`，编辑 `shared/AGENT_REGISTRY.md`，配置你的 Agent 列表和权限。

### 4.3.3 配置 Worker Agent 权限约束

参考 `templates-zh/AGENT_CONSTRAINTS.md`，确保每个 Worker Agent 了解其权限边界。

### 4.3.4 配置 Worker Agent SOUL.md

为每个 Worker Agent 创建 `workspace-{label}/SOUL.md`，使用 `templates-zh/SOUL_WORKER.md` 作为模板。

---

## 4.4 验证设置

### 4.4.1 文件系统验证

```bash
# 检查目录结构
ls -la ~/.openclaw/workspace/
ls -la ~/.openclaw/workspace-{label}/
ls -la ~/.openclaw/shared/

# 检查文件
head ~/.openclaw/shared/AGENT_REGISTRY.md
head ~/.openclaw/workspace/AUDIT_LOG.md
```

### 4.4.2 权限验证

```bash
# 测试 Worker 访问自己的 workspace（应该成功）
cat ~/.openclaw/workspace-{label}/SOUL.md

# 测试 Worker 读取 shared（应该成功）
cat ~/.openclaw/shared/AGENT_REGISTRY.md

# 测试 Cron 创建（需要实际执行）
openclaw cron add --name "{label}-测试任务" --at "5m" ...
```

### 4.4.3 审计验证

```bash
# 检查缓冲文件
cat ~/.openclaw/shared/AUDIT_BUFFER.md

# 检查日志文件
cat ~/.openclaw/workspace/AUDIT_LOG.md

# 检查快照
ls -la ~/.openclaw/workspace/.cron_snapshot.json
```

---

## 4.5 故障排查

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 无法创建 Cron | Gateway 未启动 | 检查 `openclaw gateway status` |
| 命名冲突 | 前缀重复 | 修改 label，更新注册表 |
| 审计不记录 | 未追加到缓冲 | 检查 AUDIT_BUFFER.md 路径 |
| 维护任务不执行 | Cron 表达式错误 | 检查语法，重新创建任务 |
