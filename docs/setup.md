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

# 复制模板文件
cp /path/to/templates/AGENT_REGISTRY.md shared/
cp /path/to/templates/AGENT_CONSTRAINTS.md shared/
cp /path/to/templates/GOVERNANCE.md shared/
cp /path/to/templates/AUDIT_BUFFER.md shared/
cp /path/to/templates/COMPLIANCE_CHECKLIST.md shared/
cp /path/to/templates/AUDIT_LOG.md workspace/
cp /path/to/templates/AUDIT_MAINTENANCE_GUIDE.md workspace/
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
cp templates/SOUL_WORKER.md ~/.openclaw/workspace-work/SOUL.md
cp templates/SOUL_WORKER.md ~/.openclaw/workspace-study/SOUL.md

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

创建 `shared/AGENT_REGISTRY.md`：

```markdown
# Agent 身份与权限注册表

## 物理隔离结构

```
~/.openclaw/
├── workspace/                  # Main Agent 私有区
├── workspace-{label}/          # 各 Agent 独立私有区
├── agents/{label}/             # Agent 元数据
└── shared/                     # 唯一共享区
```

## Agent 列表

| Label | 名称 | Cron 前缀 | 工作区路径 | 权限 |
|-------|------|-----------|------------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 |
| {label1} | Agent 1 | `{label1}-` | `workspace-{label1}/` | 自治 |
| {label2} | Agent 2 | `{label2}-` | `workspace-{label2}/` | 自治 |

## 文件系统权限边界

### 私有区（严格隔离）
- **Main**: `~/.openclaw/workspace/` — 仅 Main 可读写
- **{label1}**: `~/.openclaw/workspace-{label1}/` — 仅 {label1} 可读写

### 共享区（全员只读，Main 可写）
- **路径**: `~/.openclaw/shared/`
- **访问规则**:
  - 所有 Agent: 只读访问
  - Main Agent: 读写权限（维护者）

### Main 的特殊权限（需自律）
- ✅ 技术上可以访问所有 workspace
- ❌ 按规则禁止随意访问其他 Agent 私有区
- ⚠️ 仅以下情况可访问:
  1. 获得明确授权
  2. 系统维护需要
  3. 调试/故障排查

## Cron 自治规则（所有 Agent 必须遵守）

### 身份识别
每个 Agent 通过当前 session 的 `label` 确定身份。

### 创建任务
- ✅ 可以创建新任务
- ⚠️ 强制：任务名必须以 `{label}-` 为前缀

### 修改/删除任务
- ✅ 只能操作以 `{label}-` 开头的任务
- ❌ 禁止：操作其他前缀的任务

### 命名空间冲突
如果尝试创建任务时发现前缀已被占用：
1. 检查是否为自己的旧任务
2. 如果是其他 Agent 的任务，联系 Main 协调
3. 禁止强行覆盖或删除他人任务

## Main Agent 的保留权限

- 查看所有任务（监督）
- 在冲突时强制干预
- 清理无前缀/僵尸任务
- 修改本注册表
- 维护审计日志（自动化）

## 审计与治理配置

| 配置项 | 路径 | 说明 |
|--------|------|------|
| 审计日志 | `~/.openclaw/workspace/AUDIT_LOG.md` | 正式审计记录 |
| 审计缓冲 | `~/.openclaw/shared/AUDIT_BUFFER.md` | Worker 上报缓冲区 |
| 治理规范 | `~/.openclaw/shared/GOVERNANCE.md` | 企业级行为准则 |
| 权限约束 | `~/.openclaw/shared/AGENT_CONSTRAINTS.md` | Worker Agent 权限定义 |
| 维护任务 | `main-审计维护`（Cron） | 每周日 9:00 自动执行 |
| 季度归档 | `~/.openclaw/archive/YYYY-Qx/` | 归档目录 |
```

### 4.3.3 配置 Worker Agent 权限约束

创建 `shared/AGENT_CONSTRAINTS.md`：

```markdown
# Agent 权限约束

**适用对象**: 所有 Worker Agent

## 核心规则

### 1. Cron 任务自治

#### ✅ 允许的操作
- 创建定时任务（必须带 `{label}-` 前缀）
- 修改自己的任务
- 删除自己的任务
- 查看所有任务（用于自检）

#### ❌ 禁止的操作
- 创建无前缀的任务
- 使用他人的前缀
- 修改/删除他人的任务

#### 命名规范
```
格式: {label}-{任务名}
```

### 2. 审计上报（强制）

执行以下操作时，**必须**追加到 `~/.openclaw/shared/AUDIT_BUFFER.md`：

| 操作 | 格式 |
|------|------|
| 创建任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_CREATE] 任务名: xxx` |
| 修改任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_UPDATE] 任务名: xxx` |
| 删除任务 | `[YYYY-MM-DD HH:MM] [{label}] [CRON_DELETE] 任务名: xxx` |

### 3. 文件系统边界

#### ✅ 可访问
- `workspace-{label}/` — 你的私有区
- `shared/` — 共享区
- `shared/AUDIT_BUFFER.md` — 审计上报

#### ❌ 禁止访问
- `workspace/` — Main Agent 私有区
- 其他 Agent 的 workspace

## 冲突处理

发现问题时：
1. 立即停止操作
2. 联系 Main Agent
3. 等待协调结果
```

### 4.3.4 配置 Worker Agent SOUL.md

为每个 Worker Agent 创建 `workspace-{label}/SOUL.md`，包含：

```markdown
# SOUL.md

## Workspace Boundaries (工作区边界)

### ✅ 可以访问的范围

| 路径 | 权限 | 用途 |
|------|------|------|
| `workspace-{label}/` | 读写 | 你的专属工作区 |
| `shared/skills/` | 只读 | 共享 skill |
| `shared/assets/` | 只读 | 公共资源 |
| `shared/knowledge/` | 读写 | 共享知识库 |
| `shared/communication/` | 读写 | Agent 间沟通 |

### ❌ 禁止访问的范围

- `workspace/` (Main Agent)
- 其他 Worker Agent 的 workspace

### Cron 任务自治权限

#### ✅ 你可以做的
- 创建任务（必须带 `{label}-` 前缀）
- 修改自己的任务
- 删除自己的任务
- 查看所有任务

#### ❌ 禁止做的
- 创建无前缀的任务
- 使用他人的前缀
- 修改/删除他人的任务

#### 审计上报责任
执行 Cron 操作时，**必须**追加到 `~/.openclaw/shared/AUDIT_BUFFER.md`：
```
[YYYY-MM-DD HH:MM] [{label}] [CRON_CREATE] 任务名: xxx
```

### 跨 Agent 协作

需要协作时，通过 `shared/communication/` 或联系 Main。
```

### 4.3.5 创建审计缓冲文件

创建 `shared/AUDIT_BUFFER.md`：

```markdown
# 审计缓冲（所有 Agent 可写）

**用途**: Worker Agent 记录关键操作
**消费方式**: Main Agent 每周自动读取并归档

---

## 记录规范

### 格式
```
[YYYY-MM-DD HH:MM] [AGENT_LABEL] [ACTION] 详情
```

### 示例
```
[2026-03-18 14:30] [work] [CRON_CREATE] 任务: work-日报提醒, schedule: 0 9 * * *
```

---

## 日志条目

```

### 4.3.6 创建审计日志文件

创建 `workspace/AUDIT_LOG.md`：

```markdown
# 审计日志

**位置**: Main Agent 私有区
**维护者**: Main Agent

---

## 日志规范

### 记录范围
- Cron 创建/修改/删除
- 跨 Agent 访问
- 权限变更

### 格式
```
[YYYY-MM-DD HH:MM] [SOURCE] [AGENT] [ACTION] 详情
```

---

## 日志条目

### 2026-Q1

```

### 4.3.7 创建维护手册

创建 `workspace/AUDIT_MAINTENANCE_GUIDE.md`，内容参考模板文件。

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
