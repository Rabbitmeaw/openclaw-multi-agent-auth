# Agent 身份与权限注册表

## 物理隔离结构

```
~/.openclaw/
├── workspace/                  # Main Agent 私有区
├── workspace-{label}/          # 各 Agent 独立私有区
├── agents/{label}/             # Agent 元数据
└── shared/                     # 唯一共享区 (本文件所在)
```

## Agent 列表

| Label | 名称 | Cron 前缀 | 工作区路径 | 权限 |
|-------|------|-----------|------------|------|
| main | Main Agent | `main-` | `workspace/` | 全局管理 |
| {label1} | Agent 1 | `{label1}-` | `workspace-{label1}/` | 自治 |
| {label2} | Agent 2 | `{label2}-` | `workspace-{label2}/` | 自治 |
| ... | ... | ... | ... | ... |

**说明**: 替换 {label1}, {label2} 为实际的 Agent 标识（如 work, study, zhangsan 等）

## 文件系统权限边界

### 私有区（严格隔离）
- **Main**: `~/.openclaw/workspace/` — 仅 Main 可读写
- **{label1}**: `~/.openclaw/workspace-{label1}/` — 仅 {label1} 可读写
- **其他 Agent**: 同理

### 共享区（全员只读，Main 可写）
- **路径**: `~/.openclaw/shared/`
- **访问规则**:
  - 所有 Agent: 只读访问（读取本文件等）
  - Main Agent: 读写权限（维护注册表）

### Main 的特殊权限（需自律）
- ✅ **技术上可以**访问所有 workspace（文件系统权限）
- ❌ **按规则禁止**随意访问其他 Agent 私有区
- ⚠️ **仅以下情况可访问**:
  1. 获得明确授权
  2. 系统维护需要
  3. 调试/故障排查
  4. 紧急安全干预

## Cron 自治规则（所有 Agent 必须遵守）

### 身份识别
每个 Agent 通过当前 session 的 `label` 确定身份。

### 创建任务
- ✅ 可以创建新任务
- ⚠️ **强制**：任务名必须以 `{label}-` 为前缀
- ✅ 示例：
  - `{label1}-任务A`
  - `{label2}-任务B`

### 修改/删除任务
- ✅ 只能操作以 `{label}-` 开头的任务
- ❌ **禁止**：操作其他前缀的任务
- 修改前必须先 `cron list`，筛选出自己前缀的任务

### 命名空间冲突
如果尝试创建任务时发现前缀已被占用：
1. 检查是否为自己的旧任务（可以 update）
2. 如果是其他 Agent 的任务，联系 Main 协调
3. 禁止强行覆盖或删除他人任务

## Main Agent 的保留权限

即使其他 Agent 自治，Main 仍保留：
- 查看所有任务（监督）
- 在冲突时强制干预
- 清理无前缀/僵尸任务
- 修改本注册表
- 维护审计日志（自动化）

## 审计与治理配置

| 配置项 | 路径 | 说明 |
|--------|------|------|
| 审计日志 | `~/.openclaw/workspace/AUDIT_LOG.md` | 正式审计记录（仅 Main 可访问） |
| 审计缓冲 | `~/.openclaw/shared/AUDIT_BUFFER.md` | Worker 上报缓冲区（全员可写） |
| 治理规范 | `~/.openclaw/shared/GOVERNANCE.md` | 企业级行为准则 |
| 权限约束 | `~/.openclaw/shared/AGENT_CONSTRAINTS.md` | Worker Agent 权限定义 |
| 维护指南 | `~/.openclaw/workspace/AUDIT_MAINTENANCE_GUIDE.md` | Main Agent 操作手册 |
| 维护任务 | `main-审计维护`（Cron） | 每周日 9:00 自动执行 |
| 季度归档 | `~/.openclaw/archive/YYYY-Qx/` | 归档目录 |
| 日志轮转 | 季度 | 保留最近 4 季度 |

## 各 Agent 必读文件

所有 Worker Agent 启动时应读取：
1. `~/.openclaw/shared/AGENT_REGISTRY.md` — 确认身份和权限
2. `~/.openclaw/shared/AGENT_CONSTRAINTS.md` — 了解约束规则
3. `~/.openclaw/shared/GOVERNANCE.md` — 了解治理规范（如适用）
